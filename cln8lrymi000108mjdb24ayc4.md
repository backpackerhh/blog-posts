---
title: "The one with a mouse jiggler in Ubuntu"
seoDescription: "Learn how to prevent your laptop from sleeping while streaming with Plex. Avoid changing power settings every time with a mouse jiggler like Keep Presence."
datePublished: Mon Oct 02 2023 08:02:25 GMT+0000 (Coordinated Universal Time)
cuid: cln8lrymi000108mjdb24ayc4
slug: the-one-with-a-mouse-jiggler-in-ubuntu
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1696187127142/786c5df6-75d5-4f27-b428-62e9e53e0c56.png
tags: ubuntu, linux, opensource, cli, mouse-jiggler

---

Lately I've been using [Plex](https://www.plex.tv/) to stream movies and TV shows from the laptop in my study to the TV in my living room. So I needed to prevent the laptop from sleeping while I was watching something.

The easiest way and probably the most obvious one was changing the _Power Settings_ configuration so the computer never goes to sleep when inactive:

![power settings configuration](https://github.com/backpackerhh/blog-posts/assets/685978/fbce08a5-a2ab-46cd-a212-2cbd499801f3)

But that's not what I really wanted since that option means that I'd have to change the configuration every time I want to watch something and change it again afterwards. Mainly because I don't want that configuration always disabled.

Therefore I looked for different options and I found that what I needed was a [mouse jiggler](https://en.wikipedia.org/wiki/Mouse_jiggler).

In this case, I installed [Keep Presence](https://github.com/carrot69/keep-presence) in Ubuntu Mate 22.04, a program that moves the mouse or presses a key when it detects the user is away from the computer. 

You can install it as follows:

```bash
$ sudo snap install keep-presence
```

Check all the options available:

```bash
$ keep-presence --help
usage: keep-presence.py [-h] [-s SECONDS] [-p PIXELS] [-c] [-m MODE]
                        [-r RANDOM RANDOM]

This program moves the mouse or press a key when it detects that you are away.
It won't do anything if you are using your computer. Useful to trick your
machine to think you are still working with it.

optional arguments:
  -h, --help            show this help message and exit
  -s SECONDS, --seconds SECONDS
                        Define in seconds how long to wait after a user is
                        considered idle. Default 300.
  -p PIXELS, --pixels PIXELS
                        Set how many pixels the mouse should move. Default 1.
  -c, --circular        Move mouse in a circle. Default move diagonally.
  -m MODE, --mode MODE  Available options: keyboard, mouse, both (mouse &
                        keyboard) and scroll; default is mouse. This is the
                        action that will be executed when the user is idle: If
                        keyboard is selected, the program will press the shift
                        key. If mouse is selected, the program will move the
                        mouse. If both is selected, the program will do both
                        actions.
  -r RANDOM RANDOM, --random RANDOM RANDOM
                        Usage: two numbers (ex. -r 3 10). Execute actions
                        based on a random interval between start and stop
                        seconds. Note: Overwrites the seconds argument.
```

For instance, if you wanna run the script every 10 minutes, you could do it with following command:

```bash
$ keep-presence -s 600
--------
15:37:22 Mouse is enabled, moving 1 pixels 
15:37:22 Running every 600 seconds
--------
15:37:22 User activity detected
--------
15:47:22 User activity detected
...
```

Notice that the script does nothing if it detects that you're using the computer.

At this point, you can go now and come back later once you've finished watching a movie or whatever.

When you're back after a while, you'll see something like this:

```bash
...
--------
15:57:22 Idle detection
15:57:22 Moved mouse to:  (3014, 194)
--------
16:07:22 Idle detection
16:07:22 Moved mouse to:  (3015, 195)
--------
16:17:22 Idle detection
16:17:22 Moved mouse to:  (3016, 196)
...
```

If you no longer need the script to keep running, press `Control + c` to quit:

```bash
^C
Bye bye ;-)
```

As you can see, **keep-presence** is a simple yet powerful [open-source script written in Python](https://github.com/carrot69/keep-presence/blob/master/src/keep-presence.py). I highly recommend you to give it a try if you are looking for a **mouse jiggler** in a Unix-based OS.

Thank you for reading and see you in the next one!