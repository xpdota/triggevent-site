---
layout: default
title: "Linux Install Instructions"
permalink: /pages/Titan-Jail/
description: Triggevent is an FFXIV addon with native Linux support (no WINE, hudkit, etc required).
---

# Linux Install

First, grab the latest release from the [Github releases page](https://github.com/xpdota/event-trigger/releases). 
The file you want is `triggevent-linux.zip`. Unzip this file somewhere.

## Java Install

You'll need to install Java 17. If you wish to do so using your normal package manager, ensure that the
`java` program is on your $PATH and points to the correct Java install.

Otherwise, you'll need to install Java to a directory called `jre` within the main Triggevent install directory.
(i.e. the `jre` directory needs to directly contain `bin`, `lib`, etc). You can symlink this to an existing install.

If you need to install Java, you can run `setup-java.sh` in the Triggevent dir, or 
[manually download Java 17](https://www.oracle.com/java/technologies/javase/jdk17-archive-downloads.html). If you manually
download, remember to rename the directory from `jdk-x.y.z` to `jre`.

## Running

Simply run `triggevent.sh`, or `triggevent-import.sh` if you wish to import a log.

## Known Issues

Overlay scaling does not work as well as on Windows. They seem to always render at the un-scaled
resolution, and then are interpolated up to the scaled size. 

The `.sh` scripts are not currently handled by the updater, and the updater will re-download the unnecessary `.exe` files if
they are deleted.

Even when overlays are in click-through mode (i.e. not editing), there is a 1x1 area near the top-left of the overlay where
clicks will still be intercepted by the overlay.

A bundled `libjawt.so` is provided, but you can make a `userdata` directory and place another `libjawt.so` in there to override
it if you are having issues with click-through working correctly.