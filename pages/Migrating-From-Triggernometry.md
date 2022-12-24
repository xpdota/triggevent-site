---
layout: default
title: "Migrating from Triggernometry"
permalink: /pages/Migrating-from-Triggernometry-FFXIV-addons/
description: If you're looking to migrate to more modern FFXIV addons for triggers and UI needs, read this guide.
---

# Migrating from Triggernometry to more modern FFXIV Addons

Whether you'd rather not have to learn about regex and log lines, or you'd just prefer
an easier and ready-made solution, it is easy to get the same functionality in an
easier-to-use form from other addons. This guide will show you what other addons will
get the job done with less effort.

This depends on your specific use cases.

## DoT Tracking

Triggevent has a very feature-rich DoT tracker.
It is tailor-made for FFXIV game mechanics, including support for multiple targets,
showing ticks, showing application delay, and more. You can read about it on the 
[DoT Tracker](/pages/Dot-Tracker.md) page. 

There are other options as well. There are Dalamud plugins that can display debuffs
directly on the enemy list. If you don't need multi-target support, there are even more
options, including Cactbot Jobs, and a Dalamud addon that displays the remaining duration
directly on your hotbar.

## Cooldown Tracking

Triggevent also has a very solid CD tracker, for both personal and party cooldowns.
It shows the time remaining until off CD, and the remaining active time when it has
been used. You can read more on the [CD Tracker](/pages/Cooldown-Tracker.md) page.

Other options I recommend include JobBars, and Cactbot Jobs once again.

## Pre-Made Duty Triggers

Triggevent has excellent triggers for newer duties (mostly Endwalker and beyond), 
as well as a few select fights from older expansions.

If you need triggers for older FFXIV content, such as Shadowbringers or Stormblood content, 
Cactbot is the most complete, and comes in a
nice, ready-to-use package - you don't have to go find repositories or exports or triggers.
Both options allow you to customize the triggers heavily without missing out on updates.
They also allow for whatever combination of on-screen text, TTS, and sound effects
you desire.

## Making Your Own Triggers

Triggevent is unlike other addons in that it actually interprets log lines and other events
for you. The "Easy Triggers" is a point-and-click way of making triggers from scratch, with
no knowledge of log lines or regex required. 

It does not have quite as much functionality as Triggernometry in this area, but the functionality
that *is* there is significantly easier to use. Here is what is currently available:
- Trigger off any of the following "rich" events, which are fully interpreted already:
  - Cast bars
  - Ability usages
    - Including the ability to trigger specifically off of crits and the like
  - A player or enemy dying
  - A buff being applied or removed
  - A snapshotted ability actually resolving
  - A tether
  - Actor control events
  - Chat lines
  - As a fallback, as well as to support directly importing vanilla ACT triggers, you can also
    use plain old log line and filter with a regex.
- Use rich conditions, such as "target of the ability is a party member", or "the debuff has an initial duration of 30 seconds"
  - You can also write custom code for your own filters, allowing you to use anything that isn't supported yet. 
    For example, `event.target.job.isTank()` would filter to only events where the target is a tank.
- [Automarkers](/pages/Automarkers.md) available as an action - no more waiting for someone else to make them (and then waiting longer for the bugs to be ironed out)
- Delays between actions
- Icons on your overlay
- Dynamic on-screen text (e.g. displaying remaining duration of a castbar or buff)

In addition, the ability to create timeline triggers - something which until now 
would require writing your own Cactbot triggers - is exposed in a nice graphical timeline editor.
You can check it out more on the [Timeline Customization](/pages/Timeline-Customization.md) page.

Lastly, if you do want to go for the most advanced triggers, and you're not afraid to get your
hands dirty with some code, you can [code your own module](https://github.com/xpdota/triggevent-example-module).
