---
layout: default
title:  "My OS upgrade adventures"
date:   2018-08-28
category: linux
tags: [ubuntu, update, upgrade]
---

# My OS upgrade adventures

*** Aka my operating system (OS) upgrade postmortem ***

__Note__: This post is about Ubuntu OS, and could hopefully apply to other Linux distributions and other OS.

__Tl;dr__: To upgrade from Ubuntu 14.04 to 18.04, back up your laptop (don't forget your browser bookmarks) and go for a fresh install of Ubuntu 18.04.

__Before reading, have you updated your run your OS update manager recently?__

## "Funny"story 😬

### Once upon a time
I remember a time not so long ago when I used to run `sudo apt-get update`* and `sudo apt-get upgrade`* each day after starting my laptop. Back then most packages were up to date at all time and all was for the best!

While daily updates might not be necessary, I was recently reminded how important an update manager is!

### Not so long ago
I am ashamed to say that I was still using Ubuntu 14.04 (Trusty Tahr) released in 2014.
[Apparently](https://wiki.ubuntu.com/Releases) its end of life is set for April 2019, but several software didn't have a latest update suited for this version already.

Anyway, I was and still am a satisfied Ubuntu user. So why not upgrade to the next stable version once the update manager told me to?

### What happened was
Well, when Ubuntu 16.04 (Xenial Xerus) came around in 2016 I heard it would have Python 3 by default while most of my Python projects use Python 2 (2.7) and I somehow thought updating would mess up too many things. While newer projects I usually did in Python 3 (3.4) in a virtualenv, I wanted to wait to upgrade Python 2.7 projects to Python 3... which I postponed and ultimately never did. And for the update manager to stop bugging me I unchecked the option to look for the latest stable version and I guess I did less and less updates as I feared new versions could break if their dependencies didn't have a new version on Ubuntu 14.04.

Now I can see how counterproductive this behaviour was. But on a day to day basis things seemed to work okay at least for a (too long) while.


## The push 🙂
As mentioned earlier several softwares didn't have a latest update available in the Ubuntu 14.04 package repository.

I can remember for example that the spell checker in Slack stopped working (and contacting support I learned this was specific to Ubuntu 14.04), I couldn't install the latest Node.js (although I used [n](https://github.com/tj/n) as a workaround), and there was a glitch when using pip3 to install Python packages while working with Python 3.6 in a virtualenv (Python 3.6 is not available for direct install in Ubuntu 14.04).
I guess those are memories soon to be forgotten, but mostly because I lost all my bookmarks of StackOverflow or Ask Ubuntu links.

The point is, it was starting to get too obvious that I should have upgraded Ubuntu. Also Ubuntu 16.04 was already "out" with the release of Ubuntu 18.04 (Bionic Beaver) in early 2018.


## "Horror story" 😱

### It's a plan...
So I finally found a moment to upgrade with minimum negative consequences on my work.
It was actually crucial for me to write down "Back up before upgrade" and "Ubuntu upgrade" as cards in my planning [Trello](trello.com) board.
At this stage I had two choices: (1) a fresh Ubuntu 18.04 upgrade or (2) a progressive upgrade to Ubuntu 16.04 and then to 18.04. With solution (2) I'd keep all my softwares installed and files/folders. That's the choice I made and thought I had actually wasted time with my backup! LoL

### ... Or is it?
That's with confidence I set out to upgrade up one version to 16.04 using the built-in `software-updater`. I started at midday thinking I could have lunch during the upgrade and go back to working (creating a one page app with React) after lunch. But how wrong was I!
It all want okay and a notification asked me to restart in order to finish the install. I clicked on "restart", but after that there where more packages to install and something that looked like a Python error.

Well, then my laptop crashed. Not [BSOD](https://en.wikipedia.org/wiki/Blue_Screen_of_Death), but black screen and restart console mode. With an error message: 
```ImportError: /usr/lib/x86_64-linux-gnu/libapt-pkg.so.5.0: symbol (...), version GLIBCXX_3.4.21 not defined in file libstdc++.so.6 with link time reference```

### So now what?
1) I realised I should have done the fresh Ubuntu 18.04 install, but I couldn't do it then as "apt-get" didn't even work to finish this install
2) I took a deep breath (to reproduce as many times as needed)
3) I used my phone to search for that error.

Luckily [others](https://askubuntu.com/questions/777803/apt-relocation-error-version-glibcxx-3-4-21-not-defined-in-file-libstdc-so-6) have had this error before and I learned I needed to install a new version of `libstdc++.so.6` for Ubuntu 16.04. I spare you the time spend to try and connect to my wifi from the terminal (using `iwconfig` and `nmcli`) with no success.

### My salvation
I headed to the nearest (opened) library to use another computer to dowload the packages needed and transfer them on an usb device.
One response from the Ask Ubuntu question linked above recommends to get two packages:
* `libstdc++6_5.4.0-6ubuntu1~16.04.4_i386.deb`
* `libstdc++6_5.4.0-6ubuntu1~16.04.4_amd64.deb`

I then mounted my usb device on my broken laptop, copied and `dpkg`-ed both packages.
After that the previous error message (with `libstdc++.so.6`) didn't appear again, but I still couldn't do a `sudo apt-get update`. I momentarily went back down the rabbit hole of trying to connect to the wifi and failing to do so.

I was saved by reading through the replies to the Ask Ubuntu link (once again). Someone said the packages needed have actually already been downloaded, but the previous error made Ubuntu crash while installing the packages and the solution is to use: `sudo apt-get install -f`.

## Epilogue 👩🏿‍💻
After that I could restart normally. A window opened to signal more packages couldn't be installed.
I wouldn't have worried too much until I plugged my earphones and realised the sound didn't work.

That's when I had too much and went for the fresh Ubuntu 18.04 (yes, the one I should have done from the start!).

So now I have that new laptop feeling (Bionic Beaver, yay!).
I had back up my files and folders, but not the softwares installed. I'm okay with that as I can now pretend to start again clean! LoL
I forgot to back up my browser bookmarks, which you might want to do. I've actually realised I was keeping tons of bookmark I didn't need.

___

References:

* In the land of Ubuntu `sudo apt-get update` and `sudo apt-get upgrade` are the basic commands for updates from the command line
* Ubuntu releases: [wiki.ubuntu.com/Releases](https://wiki.ubuntu.com/Releases)
* Node.js versions manager: [https://github.com/tj/n](https://github.com/tj/n)
* Ubuntu upgrade notes from [Ubuntu documentation](https://help.ubuntu.com/community/UpgradeNotes)
* That `libstdc++.so.6` error on [Ask Ubuntu](https://askubuntu.com/questions/777803/apt-relocation-error-version-glibcxx-3-4-21-not-defined-in-file-libstdc-so-6)