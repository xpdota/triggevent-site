---
layout: default
title: "The Omega Protocol Ultimate (TOP)"
permalink: /pages/triggers/The-Omega-Protocol-Ultimate/
description: Triggevent has support for The Omega Protocol (Ultimate) with Triggers and Automarks
---

# The Omega Protocol Ultimate (TOP)

Like every duty, triggers come from one of a few places:
1. Progging the fight myself
2. Triggers contributed by others
3. Log files (and sometimes videos) contributed by others
4. Looking at triggers added to other projects, such as Cactbot

#1 takes time. I have to prog the fight just like everyone else. I typically make use of
[Easy Triggers](../tutorials/Easy-Triggers.md) during the raid, since it is very easy to
tab out, search for an event, and have a trigger made in a few clicks. Then, after the raid
night, I can turn them into fully blown triggers in the codebase, and release them to the public.

[//]: # (#2 and #3 are where you can help. If you happen to know Java, or at least want to learn, you can)

[//]: # (submit a [pull request]&#40;https://github.com/xpdota/event-trigger/pulls&#41; with new triggers. If you)

[//]: # (made any Easy Triggers, you can also send them my way, and I can put them in the codebase. Lastly,)

[//]: # (even if you don't make any triggers, log files are much appreciated, especially if there's also)

[//]: # (a VoD to go along with it. You can post them on the )

[//]: # ([discussions]&#40;https://github.com/xpdota/event-trigger/discussions&#41; area, or send them private)

[//]: # (via discord &#40;https://discord.gg/jxk24jC66r&#41;.)


Without further ado, here's what's currently available:

## Phase 1

- Looper
  - Number callouts
  - Tower/tether callouts
  - Optional Automarker
- Pantokrator
  - Number callouts
  - Out/in callouts
  - Tank/cleave bait calls
  - Optional Automarker

## Phase 2

- Mid/Remote Glitch
- Tether Partner, PS Icon
  - Optional Automarker
- M/F In/Out
- Pile Pitch/Beyond Defense/Flares

## Phase 3

- Sniper Cannon/High-Powered Sniper Cannon
  - Optional Automarker
- Hello World
  - Separate callouts for each debuff
  - Can be modified based on defamation color
  - Separate callouts for the last round
  - Want to make it even more obvious? Set the defamation/stack callouts to red/blue accordingly.
- Monitor
  - Callouts, including which direction the boss is cleaving
  - Optional Automarker

## Phase 4
- Blue Screen: Basic Callouts, including who has stacks, and when to move in-out

## Phase 5
- Solar Ray (Buster)
- Run: Dynamis (Delta Version)
  - Simple Callouts
  - Simple Near/Distant world Automark
- Run: Dynamis (Sigma Version)
  - Simple Callouts
  - Comprehensive Automark
- Run: Dynamis (Omega Version)
  - Simple Callouts
  - Comprehensive Auto marker

# Omega Protocol Automarkers

Each automarker can be independently enabled or disabled. Some allow for customization of the job priority,
and may also allow customization of which marker each role should receive.

If those are not enough for you, you can also develop your own, using one of many options. For simple tasks
(such as a simple mark on people who have a debuff), you can use 
[easy trigger automarkers](../Automarkers.md#making-your-own-automarks-using-easy-triggers). If you have
more complex needs, such as marking people who are *not* targeted with something, or need some kind of priority,
you can use [scripted automarkers](../Automarkers.md#making-your-own-automarks-with-scripts) or
even [develop your own module](../Automarkers.md#making-automarks-in-the-code--as-a-separate-module).