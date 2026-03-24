---
title: "The one about multi-API support in legacy code"
seoDescription: "Learn how I refactored a legacy Sinatra proxy and React SPA to support multiple APIs with improved testing and essential dev-to-production parity."
datePublished: 2026-03-23T20:14:14.629Z
cuid: cmn3mjazm00gy2flk4ge22rtz
slug: the-one-about-multi-api-support-in-legacy-code
cover: https://cdn.hashnode.com/uploads/covers/642d433eeaad3d174f737099/30c7fd89-597f-4546-9b33-3814549dd7b3.png
tags: refactoring, proxy, ruby, api, spa, reactjs, testing, legacy-code, sinatrarb

---

In a [previous post](https://davidmontesdeoca.dev/the-one-about-a-new-project-working-for-an-american-fintech) I mentioned I had been working on replicating some functionality for collecting beneficiaries data in an old SPA.

**The code in that application was quite legacy and highly coupled to an existing API.** It was poorly tested, and even those tests did not have a make command to run locally nor were they running in the CI pipeline.

It consisted of a [Sinatra](https://sinatrarb.com/) application that acts as a proxy between a React frontend and a backend API. For a long time, this proxy only knew about one API, so everything was built around that single dependency.

Then I needed to add support for a new API. But first, I had to isolate anything that was coupled to the existing API, thus making room for the upcoming changes.

> In the examples below, I have omitted code that is unnecessary to understand the process, such as logging and observability instrumentation.

## The initial setup

The backend application is very simple. The entry point is `config.ru`, which maps routes to different Rack apps via a Dispatcher:

```ruby
# system/infrastructure/dispatcher.rb
module Infrastructure
  Dispatcher = Rack::Builder.app do
    map '/' do
      run System::Server
    end

    map '/api' do
      run System::Proxy
    end
  end
end
```

The server handles static file serving for the React SPA:

```ruby
# system/server.rb
module System
  class Server < Sinatra::Base
    set root: File.expand_path('..', __dir__)

    not_found do
      render_static_file
    end

    get '/' do
      render_static_file
    end

    private

    def render_static_file
      File.read(File.join('public', 'index.html'))
    end
  end
end
```

The proxy delegates to a request handler, where the actual API proxying happens:

```ruby
# system/request_handler.rb
module System
  class RequestHandler
    UNAUTHORIZED_CODE = 401
    ALLOWED_PATHS = %w(
      /beneficiary_collections
    )
    ALLOWED_METHODS = %w(GET PUT PATCH POST)
    HEADERS = { 'Content-Type' => 'application/json' }

    def initialize(env)
      @request = Rack::Request.new(env)
    end

    def call
      return render(UNAUTHORIZED_CODE, { error: 'Unauthorized' }.to_json) if invalid_path && invalid_request_method

      response = # HTTParty logic

      render(response.code, response.body)
    end

    private

    def invalid_path
      !ALLOWED_PATHS.include?(path)
    end

    def invalid_request_method
      !ALLOWED_METHODS.include?(@request.request_method)
    end

    def render(status, body)
      [status, HEADERS, [body]]
    end

    def api_url
      ENV['API_BASE_URL']
    end

    # other private methods omitted
  end
end
```

If you look closely at the `call` method, the guard clause uses `&&` instead of `||`. This means it only returns *unauthorized* when both the path and the method are invalid, not when either one is. This was a bug that was present from the start and that I would fix later as part of this process.

Notice that `api_url` always returns the same environment variable regardless of the request path. The whole request handler assumes there is only one API.

## First changes: routing through the proxy

Here is something I had not noticed before: **in the local development environment, the frontend was bypassing the proxy entirely**. The webpack dev server was configured to send API requests directly to the API stub.

The webpack dev server configuration was as follows:

```javascript
// client/webpack/webpack.development.js
const API_BASE_URL = process.env.API_BASE_URL || 'http://beneficiary_collections_api:5300';

module.exports = {
  // ...
  devServer: {
    host: '0.0.0.0',
    port: 3001,
    allowedHosts: 'all',
    historyApiFallback: true,
    proxy: {
      '/api': {
        target: API_BASE_URL,
        pathRewrite: { '^/api': '' }
      },
      // ...
    }
  }
}
```

The `pathRewrite` stripped the `/api` prefix and sent requests straight to the stub. This meant the Sinatra proxy was never involved in local development. Consequently, **you cannot test routing if you are skipping the proxy**.

To fix this, I added a backend service to `docker-compose.yml` and renamed `app` to `frontend`:

```yaml
services:
  frontend:
    ports:
      - 3001:3001
    depends_on:
      - backend
    command: npm start

  backend:
    ports:
      - 8080:8080
    depends_on:
      - beneficiary_collections_api
    command: bundle exec rerun --dir . --pattern "**/*.{rb}" --background -- rackup --port=8080 -o 0.0.0.0 config.ru

  beneficiary_collections_api:
    build:
      context: ./stubs/beneficiary_collections_api
    ports:
      - 5300:5300
    command: bundle exec rerun --dir . --background -- rackup --port=5300 -o 0.0.0.0 config.ru
```

I installed the [rerun gem](https://github.com/alexch/rerun) so the backend application would automatically restart when any Ruby file changed.

> Note that the page still needs to be reloaded in the browser. Until the backend is fully reloaded, requests will return a 504 error.

Then I updated the webpack dev server to point at the backend instead of the API stub directly:

```javascript
// client/webpack/webpack.development.js
const BACKEND_URL = process.env.BACKEND_URL || 'http://backend:8080';

module.exports = {
  // ...
  devServer: {
    host: '0.0.0.0',
    port: 3001,
    allowedHosts: 'all',
    historyApiFallback: true,
    proxy: {
      '/api': {
        target: BACKEND_URL
      },
      // ...
    }
  }
}
```

No more `pathRewrite`. The `/api` prefix is now kept as-is, and the backend proxy handles it from there. This matches how the application works in staging and production.

Finally, I refactored the scarce tests to route them through the `Dispatcher` instead of testing the proxy in isolation, and added the make command to be able to run those tests locally:

```ruby
# spec/requests/api/proxy_spec.rb
RSpec.describe 'Proxy' do
  include Rack::Test::Methods

  def app
    Infrastructure::Dispatcher
  end

  context 'when beneficiary_collections' do
    let(:base_url) { 'https://beneficiary-collections.lol' }

    before do
      allow(ENV).to receive(:[]).with('API_BASE_URL').and_return(base_url)

      stub_request(:get, "#{base_url}/beneficiary_collections")
        .to_return(status: 200, body: { message: 'Success' }.to_json)

      stub_request(:get, "#{base_url}/beneficiary_collections/invalid")
        .to_return(status: 401, body: { message: 'Unauthorized' }.to_json)
    end

    context 'when the URL is allowed' do
      it 'returns a 200 status code' do
        get '/api/beneficiary_collections'

        expect(last_response.status).to eq(200)
      end
    end

    context 'when the URL is not allowed' do
      it 'returns a 401 status code' do
        get '/api/beneficiary_collections/invalid'

        expect(last_response.status).to eq(401)
      end
    end
  end
end
```

## Isolating the old API

I applied a set of changes in both the frontend and the backend.

The key backend change was making `api_url` conditional in the request handler instead of returning always the same environment variable (properly renamed below):

```ruby
# system/request_handler.rb
def api_url
  if path.start_with?('/beneficiary_collections')
    ENV['BENEFICIARY_COLLECTIONS_API_BASE_URL']
  end
end
```

The tests were properly updated, too, adding a specific context for the beneficiary collections API.

On the frontend side, the template that renders the beneficiary collection form was extracted from the page where it was being rendered, so it could be easily duplicated later. I also updated the existing test suite using [React Testing Library](https://testing-library.com/docs/react-testing-library/intro/), adding a new context to ensure the extracted components still behaved correctly in isolation.

## Adding the new API

With the groundwork done, adding the new API was straightforward.

On the React side, a new route was added:

```javascript
// client/src/pages/App/App.js
import Home from 'pages/Home';
import CollectionRequestsHome from 'pages/CollectionRequestsHome';

function App() {
  return (
    <Router>
      <ContextWrapper>
        <Switch>
          <Route exact path="/" component={Home} />
          <Route exact path="/collection_requests" component={CollectionRequestsHome} />
        </Switch>
      </ContextWrapper>
    </Router>
  );
}

export default App;
```

All components were duplicated and adapted to the collection requests form, including the endpoints specific to the new API. Every change on the frontend was accompanied by new unit and integration tests.

On the backend, the request handler received several improvements:

*   All constants are frozen.
    
*   The status code for invalid requests was changed from 401 to 404, which is more semantically correct.
    
*   The validation methods became proper predicate methods.
    
*   An attribute reader was added for the request.
    
*   The `api_url` method now routes based on the request path prefix.
    

```ruby
# system/request_handler.rb
module System
  class RequestHandler
    NOT_FOUND_STATUS_CODE = 404
    ALLOWED_PATHS = %w[
      /beneficiary_collections
      /collection_requests
    ].freeze
    ALLOWED_METHODS = %w[GET PUT PATCH POST].freeze
    HEADERS = { 'Content-Type' => 'application/json' }

    def initialize(env)
      @request = Rack::Request.new(env)
    end

    def call
      if invalid_path_info? || invalid_request_method?
        return render(NOT_FOUND_STATUS_CODE, { error: 'Not found' }.to_json)
      end

      response = # HTTParty logic

      render(response.code, response.body)
    end

    private

    attr_reader :request

    def invalid_path_info?
      !(request.path_info.match?(Regexp.union(ALLOWED_PATHS)))
    end

    def invalid_request_method?
      !(ALLOWED_METHODS.include?(request.request_method))
    end

    def render(status, body)
      [status, HEADERS, [body]]
    end

    def api_url
      if path.start_with?('/beneficiary_collections')
        ENV['BENEFICIARY_COLLECTIONS_API_BASE_URL']
      elsif path.start_with?('/collection_requests')
        ENV['COLLECTION_REQUESTS_API_BASE_URL']
      end
    end

    # other private methods omitted
  end
end
```

The server needed a new route too:

```ruby
# system/server.rb
get '/collection_requests/' do
  render_static_file
end
```

That trailing slash on `get '/collection_requests/'` will come back to haunt me.

A new stub application was added for the collection requests API, and `docker-compose.yml` was updated to include it:

```yaml
services:
  collection_requests_api:
    build:
      context: ./stubs/collection_requests_api
    ports:
      - 5301:5301
    command: bundle exec rerun --dir . --background -- rackup --port=5301 -o 0.0.0.0 config.ru
```

A new context block for the collection requests API was added to the proxy test, mirroring the beneficiary collections one.

## The trailing slash problem

The code was working perfectly locally. Tests were green. It got merged and deployed to staging. And then **the collection requests form did not render**.

The issue turned out to be a trailing slash added to the route, while React router navigated to `/collection_requests`, without the trailing slash.

Sinatra does not treat these the same way by default, and it is well documented in [their FAQ section](https://sinatrarb.com/faq.html#slash).

The fix was to make the route accept both:

```ruby
# system/server.rb
get '/collection_requests/?' do
  render_static_file
end
```

The `/?` at the end makes the trailing slash optional. The `not_found` handler was also restored so that unknown routes would still serve the SPA. It returns a 404 status code so the React app can show its own not found page.

On the frontend, the webpack dev server configuration also changed. The `historyApiFallback` option was replaced with a custom middleware that validates routes and handles trailing slashes:

```javascript
// client/webpack/webpack.development.js
const VALID_PATHS = ['/', '/collection_requests'];

module.exports = {
  historyApiFallback: false,
  setupMiddlewares: (middlewares, devServer) => {
    devServer.app.get('*', (req, res, next) => {
      if (req.headers.accept?.includes('text/html')) {
        const normalizedPath = req.path.replace(/\/$/, '') || '/';

        if (!VALID_PATHS.includes(normalizedPath)) {
          res.status(404);
        }

        req.url = '/index.html';
      }

      next();
    });

    return middlewares;
  },
```

Not very elegant, but it works.

The React app also got a catch-all route for unknown paths, with a `NotFound` component:

```javascript
// client/src/pages/App/App.js
import NotFound from 'pages/NotFound';

// ...
<Route path="*" component={NotFound} />
```

I also added minimal testing for the server to cover both with and without trailing slash:

```ruby
# spec/requests/server_spec.rb
RSpec.describe 'Server' do
  include Rack::Test::Methods

  def app
    Infrastructure::Dispatcher
  end

  before do
    allow(File).to receive(:read).and_call_original
    allow(File).to receive(:read).with(File.join('public', 'index.html')).and_return('html content')
  end

  context 'when path is /' do
    it 'returns a 200 status code and HTML content' do
      get '/'

      expect(last_response.status).to eq(200)
      expect(last_response.headers['Content-Type']).to include('text/html')
    end
  end

  context 'when path is /collection_requests' do
    it 'returns a 200 status code and HTML content' do
      get '/collection_requests'

      expect(last_response.status).to eq(200)
      expect(last_response.headers['Content-Type']).to include('text/html')
    end
  end

  context 'when path is /collection_requests/' do
    it 'returns a 200 status code and HTML content' do
      get '/collection_requests/'

      expect(last_response.status).to eq(200)
      expect(last_response.headers['Content-Type']).to include('text/html')
    end
  end

  context 'when path is invalid' do
    it 'returns a 404 status code and HTML content' do
      get '/invalid'

      expect(last_response.status).to eq(404)
      expect(last_response.headers['Content-Type']).to include('text/html')
    end
  end
end
```

To prevent this from happening again, I added some make commands to run the application in production mode locally:

```yaml
# docker-compose.prod.yml
services:
  app:
    ports:
      - 8080:80
    depends_on:
      - beneficiary_collections_api
      - collection_requests_api
    environment:
      - NODE_ENV=production

  beneficiary_collections_api:
    build:
      context: ./stubs/beneficiary_collections_api
    ports:
      - 5300:5300
    volumes:
      - ./stubs/beneficiary_collections_api:/opt/stubs
    command: bundle exec rerun --dir . --background -- rackup --port=5300 -o 0.0.0.0 config.ru

  collection_requests_api:
    build:
      context: ./stubs/collection_requests_api
    ports:
      - 5301:5301
    volumes:
      - ./stubs/collection_requests_api:/opt/stubs
    command: bundle exec rerun --dir . --background -- rackup --port=5301 -o 0.0.0.0 config.ru
```

From now on, catching the kind of failure caused by trailing slashes locally is way easier.

## Conclusion

Adding support for a new API provided an opportunity to harden a legacy application. We moved from a poorly tested setup that bypassed its own proxy locally to a robust environment with proper routing and test coverage.

The challenges I faced, from the logic bug in the guard clause to the trailing slash discrepancy, demonstrate why dev-to-production parity is essential. With these changes in place, the application is no longer tied to a single dependency, and the team can now develop and test new features with the confidence that "working locally" actually means "working in production."

Thank you for reading and see you in the next one!