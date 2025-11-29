---
title: "The one about adding borders between columns in a grid"
seoDescription: "Learn how to add borders between grid columns using CSS and Tailwind CSS with practical examples "
datePublished: Sat Nov 29 2025 11:58:57 GMT+0000 (Coordinated Universal Time)
cuid: cmik8n915000102l5gdof5eke
slug: the-one-about-adding-borders-between-columns-in-a-grid
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1764417508943/7deb490d-8389-4aed-bab2-c971d85968b6.png
tags: css, html, tailwindcss, til, tailwind, tailwind-css

---

Here begins a new blog series where I plan to share useful tips I discover in my day-to-day development work.

--- 

Translating a Figma design into code for the [new project I am currently working on](https://blog.davidmp.es/the-one-about-a-new-project-working-for-an-american-fintech), I encountered a specific UI requirement: a container with 3 column sections separated by vertical borders. The catch was that these borders could not span the full height of the container; they needed inset padding at the top and bottom.

<img width="500" height="329" alt="Image" src="https://github.com/user-attachments/assets/5bffa209-be51-4ffe-9512-741ab5af7b07" />

Right away, it was clear this was a perfect use case for the wonderful CSS [grid](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Properties/grid) property. However, exactly how to achieve those inset borders appearing between the columns was less obvious.

Thanks especially to the invaluable help found in [this Reddit thread](https://www.reddit.com/r/webdev/comments/15w6ptw/how_to_get_borders_between_columns/) and [this video](https://www.youtube.com/watch?v=QjddVRthBrU), I landed on a clean solution involving the [:after](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Selectors/::after\) [pseudo-element](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Selectors/Pseudo-elements).

I created a CodePen demonstrating this using plain HTML and CSS:

<iframe height="300" style="width: 100%;" scrolling="no" title="Borders between columns in grid container using CSS" src="https://codepen.io/backpackerhh/embed/vEGWPxz?default-tab=html%2Cresult" frameborder="no" loading="lazy" allowtransparency="true">
  See the Pen <a href="https://codepen.io/backpackerhh/pen/vEGWPxz">
  Borders between columns in grid container using CSS</a> by David Montesdeoca (<a href="https://codepen.io/backpackerhh">@backpackerhh</a>) on <a href="https://codepen.io">CodePen</a>.
</iframe>

In my specific case, I am not working with plain CSS but rather [Tailwind CSS](https://tailwindcss.com/), so I also created a CodePen adapting the solution to that framework:

<iframe height="300" style="width: 100%;" scrolling="no" title="Borders between columns in grid container using Tailwind" src="https://codepen.io/backpackerhh/embed/NPNwJwP?default-tab=html%2Cresult" frameborder="no" loading="lazy" allowtransparency="true">
  See the Pen <a href="https://codepen.io/backpackerhh/pen/NPNwJwP">
  Borders between columns in grid container using Tailwind</a> by David Montesdeoca (<a href="https://codepen.io/backpackerhh">@backpackerhh</a>) on <a href="https://codepen.io">CodePen</a>.
</iframe>

> Click on HTML button above to properly see the result

Regarding the Tailwind example, I want to highlight a specific decision. My initial intention was to define the utility classes inline directly on the HTML elements, as is standard Tailwind practice.

However, combining the [:not](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Selectors/:not\) and [:last-child](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Selectors/:last-child\) [pseudo-classes](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Selectors/Pseudo-classes) with the `:after` pseudo-element seemed to prevent Tailwind from generating the correct CSS rule. Therefore, I opted to use the [@apply directive](https://tailwindcss.com/docs/functions-and-directives#apply-directive) instead and keep the HTML clean.

Finally, because this is a static design with exactly 3 columns, I briefly considered using [:before](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Selectors/::before\) and `:after` pseudo-elements only on the middle column. However, applying a right border to every item except the last one felt like a more robust approach. It ensures the solution remains scalable should a fourth column be added in the future.

There are surely thousands of ways to achieve this. If you have an interesting alternative method, feel free to share it with me.

Thank you for reading and see you in the next one!

---

%%[buy-me-a-coffee]
