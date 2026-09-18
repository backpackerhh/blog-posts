---
title: "The one about fixing Spotify playback issues on Ubuntu"
seoDescription: "Bypass Snap sandboxing audio conflicts on Ubuntu 24.04 LTS. A quick guide to routing the Spotify client through a PulseAudio wrapper script."
datePublished: 2026-09-18T21:32:31.304Z
cuid: cmu7h5gad00000agmb16dfjfv
slug: the-one-about-fixing-spotify-playback-issues-on-ubuntu
cover: https://cdn.hashnode.com/uploads/covers/642d433eeaad3d174f737099/8524db67-c10e-4f34-92a1-e3c065c2b590.png
tags: ubuntu, linux, spotify, pipewire, pulseaudio, snap, ubuntu-2404

---

After installing the latest software updates on Ubuntu, the audio in the Spotify client completely stopped working. Every track I attempted to stream would trigger a **"Can't play the current song"** error after a few seconds. Because audio was working perfectly in every other application, I knew the issue was specific to Spotify.

After searching for a solution online, I discovered that recent updates to the Spotify client transitioned the application to use [PipeWire](https://pipewire.org/) for low-latency audio delivery. However, running the app on **Ubuntu 24.04 LTS** exposed a critical flaw: the isolated Snap container lacks the system permissions to connect to PipeWire natively, breaking the audio pipeline entirely.

Official proposals on the [Snapcraft Forum](https://forum.snapcraft.io/t/request-for-interface-pipewire-auto-connect-for-spotify/50974) confirm that the Snap container still lacks pre-approved autoconnect privileges.

The cleanest, most straightforward fix is to bypass the broken native system routing and force Spotify back to the reliable PulseAudio API backend wrapper by appending the `--audio-api=pulseaudio` flag. This tells the app to safely route sound through Ubuntu's built-in *PipeWire-to-PulseAudio* compatibility bridge.

However, ensuring this fix worked universally across the **applications menu**, the **terminal**, and the **Alt + F2 launcher** revealed that different entry points in Ubuntu resolve executable paths differently. Let me show you how I built a centralized workaround to bind all three entry points to a single fix.

## Step 1: Create a centralized global wrapper script

Because the **Alt + F2 launcher** functions as a direct binary runner, it does not recognize user shell aliases. It does, however, look inside `/usr/local/bin` during path execution, so we can create a central override script:

```bash
codium /usr/local/bin/spotify
```

Inside the file, paste the following script. It explicitly calls the Snap version of Spotify and attaches the required audio API flag:

```bash
#!/bin/bash
/snap/bin/spotify --audio-api=pulseaudio "$@" > /dev/null 2>&1 &
disown
```

*Note: Because `/usr/local/bin` is a restricted system directory, you will need root privileges to save this file.*

Once saved, make it executable via the terminal:

```bash
sudo chmod +x /usr/local/bin/spotify
```

### Running in the background with `disown`

Launching a graphical app from the terminal normally floods the screen with warning logs and locks up your cursor. You can free up the cursor temporarily by appending an ampersand:

```bash
spotify &
```

Still, closing that terminal tab later will instantly kill the application. To prevent this, the script silences the log spam and calls `disown`. This instructs the terminal to drop Spotify from its active jobs table, keeping the music playing even after the terminal window is closed.

## Step 2: Fix the applications menu icon

The applications menu reads directly from its own desktop configuration files rather than binary paths. To force the icon to follow our new script rules, create a local shortcut override:

```bash
codium ~/.local/share/applications/spotify_spotify.desktop
```

Paste this desktop configuration inside:

```text
[Desktop Entry]
X-SnapInstanceName=spotify
Type=Application
Name=Spotify
GenericName=Music Player
Icon=/snap/spotify/current/usr/share/spotify/icons/spotify-linux-128.png
X-SnapAppName=spotify
Exec=/usr/local/bin/spotify %U
Terminal=false
MimeType=x-scheme-handler/spotify;
Categories=Audio;Music;Player;AudioVideo;
StartupWMClass=spotify
```

Save the file, then verify its configuration syntax:

```bash
desktop-file-validate ~/.local/share/applications/spotify_spotify.desktop
```

If the validation checks out clean, refresh your user shortcut database so Ubuntu prioritizes the override over the system default icon:

```bash
update-desktop-database ~/.local/share/applications/
```

## Step 3: Fix the terminal command lookup

To ensure your terminal prioritizes our new wrapper script over the default Snap execution path, we need to declare an explicit rule. Because I use [Zsh](https://zsh.org) as my primary shell, we can append a permanent command alias to the `.zshrc` configuration file:


```bash
echo "alias spotify='/usr/local/bin/spotify'" >> ~/.zshrc
```

Next, reload your current shell profile to apply the change instantly without opening a new terminal window:

```bash
source ~/.zshrc
```

## The final routing blueprint

With everything linked up, all entry points on the OS now seamlessly cascade into the same fix:

| Trigger method             | Target triggered                | Path resolution                         |
| :------------------------- | :------------------------------ | :-------------------------------------- |
| **Alt + F2 launcher**      | Reads binary path directly      | Runs `/usr/local/bin/spotify`           |
| **Terminal window**        | Triggered by Zsh alias rule     | Runs `/usr/local/bin/spotify`           |
| **Applications menu icon** | Reads local `.desktop` launcher | Evaluates `Exec=/usr/local/bin/spotify` |

Dealing with path conflicts and sound architectures in closed-source apps is a common Linux headache. Hopefully, this simple workaround saves you time if your media setup breaks down after a system upgrade.

Thank you for reading and see you in the next one!
