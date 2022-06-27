# Frequently Asked Questions

## Where is Data Stored?

You can access these directories via the buttons on the 'Advanced' tab.

Install Dir: Wherever you extracted the zip file

User Data Dir: By default, `%appdata%\Triggevent`. Within this directory:
* `triggevent.log`: The debug log file. Useful to attach if you encounter a bug, but be warned it may contain player names or other sensitive data.
* `triggevent.properties`: Your settings
* `triggevent.properties.backup`: A backup copy of your settings
* `triggevent-testing.properties` (and .backup): When running from an IDE, this acts as an alternate properties file so that you don't have to worry
about breaking your real settings while developing things
* `sessions` directory: If you enable the 'Save to Disk' option, this is where sessions are saved
* `userscripts` directory: Contains any custom Groovy scripts you have written within the UI. 


## I Just Downloaded it, but Some Things Seem to be Missing

Run the updater. 

## I Tried to Update, but it Froze

This is usually anti-virus related. It is most likely just scanning the updater before allowing it to run. Give it a few minutes. 

If that doesn't work, reboot and run `triggevent-upd.exe` (the updater) directly.

## What is 'NPC ID'?

Every NPC has an 'NPC ID' and 'NPC Name ID'. This is NOT the entity ID (which is upwards of 0x10000000 for players, and 0x40000000 for NPCs). Unlike
entity IDs, NPC IDs are stable. For example, in DSR Ultimate, Hraesvelgr always has an NPC ID of 12613. 'Fake' actors in ShB and beyond tend to always
have an NPC ID of 9020. 

The NPC Name ID is also stable, but governs the name. This lets you have a stable identifier for NPC names, *regardless of client language*. However,
fake actors will usually have the same name ID as the real one. 

The reason you likely haven't heard of these is because they're not in the log line where the NPC actually *does* something - that's the entity ID.
NPC IDs are only in 03-lines (AddCombatant), and the getCombatants data from ACT/OverlayPlugin. As such, most trigger solutions don't make use of it.
However, it is a very convenient way to uniquely identify bosses, as well as separating "real" actors from "fake" ones.

### Don't Rely on NPC Names for Fake Actors

NPC names for fake actors may change on the fly. Often times, this change occurs at the same time as an interesting event (e.g. an ability cast),
but whether ACT puts the old name or the new name in the log line is not something that can be relied upon. This goes for both the literal name, as
well as the NPC Name ID (in Triggevent, the name ID will almost always be the old name).

## Why Isn't This a Dalamud Plugin?

Or: Why isn't it all done within an ACT plugin?

There are two main reasons. The first is that like [Remote Cactbot](https://github.com/quisquous/cactbot/blob/main/docs/RemoteCactbot.md), it can run
fully remotely to support your console friends (or for 9th manning). OverlayPlugin is great at enabling this. Dalamud and ACT plugin interface, not so much.

The second is that I'd still end up needing to make a standalone version, due to replays. If my data source is an ACT log file or an FFLogs report,
it doesn't make sense to be running within the game.
