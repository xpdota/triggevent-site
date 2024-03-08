---
layout: default
title: "Triggevent Groovy Scripting"
permalink: /pages/groovy/Groovy-Introduction/
description: Triggevent supports Groovy Scripts which allow you to make powerful yet simple triggers.
---

# What is Groovy?

[Groovy](https://groovy-lang.org/) is a programming language which is interoperable with Java. This makes it perfect
for exposing a scripting interface for Triggevent.

There are [some examples](https://github.com/xpdota/event-trigger/wiki/Groovy-Examples) on the Wiki.

Here's a crash course on how the integration works.

## Intro

First, head over to the Groovy tab. The default script is 'Scratch', which is not saved (except when you "Save As").
You can play around here. For example, to see what global variables are currently available, delete the existing text,
and write `globals.variables`. Then, click Execute (or press Ctrl-Enter). Below, you will see a list of all available
global variables.

### The Container

Triggevent internally uses PicoContainer for its dependency injection. All of these are also inserted into the Groovy
global variables, as you see from running that command. The variable name will be the name of the class, but with the
first letter lowercase. Some of these also have shortcuts, like how `xivStateImpl` is also available under the name
`state`.

### Groovy vs Java

I won't get into all the details, but Groovy has a lot of syntactical sugar compared to Java. For example, if you have
a method `thing.getFoo()` (or `thing.isFoo()`), you can simply write `thing.foo` instead. Likewise, `thing.setFoo(value)` 
can be simplified to `thing.foo = value`. 

Groovy Strings are another neat feature. Single-quoted strings are treated as literal, but double-quoted and triple-double 
quoted strings allow interpretation of values within a `${ }` block. 

### Quick Example

Now, fire up the game if you haven't already, and run another script: `state.player`. Now, you'll see some information
about your player down below, including all the properties of the object representing your player character. You can 
keep drilling down. For example, `state.player.job.healer`. It should return `true` 
or `false` depending on whether you are currently playing a healer job.

Now, let's make a simple conditional, along with some Groovy strings:

```groovy
if (state.player.job.healer) {
	eventMaster.pushEvent(new TtsRequest("${state.player.job.friendlyName} is a healer"))
}
else {
	eventMaster.pushEvent(new TtsRequest("${state.player.job.friendlyName} is a not healer"))
}
```

Your ACT should play a TTS message: either "(your current job) is a healer", or "(your current job) is not a healer".

# Triggers

You can now do full-blown triggers from within a Groovy script.

Important Note: The script will do nothing until it is run. After pasting the script,
you can run it and it will work until you close the program. To make it persistent, save the script and check the
"run on startup" box.

Here is an example of a simple (non-sequential) trigger:

```groovy
groovyTriggers.add {
	named "foo" 
	type AbilityCastStart 
	when { it.abilityIdMatches(0x5EF8) } 
	then { event, context -> 
		context.accept(new TtsRequest(event.ability.name)) 
		log.info "bar"
	}
}
```

# Sequential Triggers

Here's a more in-depth trigger. This triggers when you start casting Broil IV. It will immediately call out TTS "foo",
and if you have the callout overlay enabled, will display a rapidly-changing number showing you how many milliseconds
are left on the cast bar, with the text having a cyan color.

Then, once the cast snapshots, it will do a text-only callout showing the amount of damage the ability did, in magenta,
with an icon taken from a status effect, replacing the previous text entirely (rather than displaying both), and will
remain there for 1000 milliseconds (1 second).

```groovy
// Don't forget the 'def' keyword
def abilityId = 0x6509
groovyTriggers.add {
    // Name should be unique
    named "Sequential Trigger Test"
    // Allow multiple concurrent invocations
    concurrency concurrent
    // Start on casting Broil IV
    when { AbilityCastStart acs -> acs.abilityIdMatches(abilityId) }
    sequence { e1, s ->
        // Call out "foo" and a constantly updating text display defined as a GroovyString,
        // plus a color in long form
        callout { tts "foo" text "${->e1.estimatedRemainingDuration.toMillis()}" color new Color(0, 255, 255) }

        // Then wait for the cast to snapshot
        snapshot = s.waitEvent(AbilityUsedEvent, aue -> aue.abilityIdMatches(abilityId))

        // Do another callout, no TTS, just text. Color in short form, plus a status icon, and a specific duration
        callout { text "${snapshot.damage}" color 255,0,255 statusIcon 0x77F replaces last duration 1000 }
    }
}
```

## Callouts

Within triggers, you have some neat tools that you can use to build callouts. You can simply write `callout "foo"` to make
a simple text and TTS callout. However, you can also do this in long form if you need more control:
```groovy
// Examples
// Different text vs TTS
callout {
    tts "Foo"
    text "Bar"
    // Use the icon from ability 0x89 (WHM Regen)
    abilityIcon 0x89
}
callout {
    // 'both' can be used when you want tts and text to be the same
    both "Stuff"
}
callout {
    tts "Foo"
    // Text that will continuously update itself - you need the '->' to make this happen. Otherwise, it will snapshot the value when the callout fires.
    text "${->e1.estimatedRemainingDuration}"
    // RGB color - you can optionally specify alpha as the last argument
    color 255,0,255
    // Use the status effect's icon for the callout
    statusIcon e1.buff.id
}
callout {
    // If you wish to declare a local variable, you must use the 'def' keyword
    def calloutText = "Foo"
    // You can use the variable directly
    tts calloutText
    // Or, you can embed it in a string
    text "${calloutText} ${->e1.estimatedRemainingDuration}"
}
callout {
    // You can also declare your tts first, and then reference it in the on-screen text:
    tts "Foo"
    text "${tts} ${->e1.estimatedRemainingDuration}"
}
```

## Callout Lifecycle

By default, a callout will last 5 seconds and then it will be removed. However, there are numerous alternatives:
```groovy
// Change the duration directly
sequence { e1, s -> 
    callout {
        both "Foo"
        // 3 seconds
        duration 3000
    }
}
// Provide a condition. The callout will be displayed as long as the condition holds true.
// Note that if you want to use globals, you must first copy them to a local variable at the top level
// of your script. In this case, you would simply write `def buffs = buffs`.
// Note that this completely overrides the "duration" directive.
sequence { e1, s ->
    callout {
        tts "Foo"
        text "${tts} ${->e1.estimatedRemainingDuration}"
        displayWhile {->buffs.originalStatusActive(e1)}
    }
}
// Specify that one callout should directly replace another callout. This can be useful for sequences of mechanics.
// All this entails is saving the first callout to a local variable, and then referencing it in the second.
sequence { e1, s ->
    def first = callout {
        tts "Foo"
        text "${tts} ${->e1.estimatedRemainingDuration}"
    }
    waitMs 2000
    callout {
        tts "Bar"
        text "${tts} ${->e1.estimatedRemainingDuration}"
        replaces first
    } 
}
// 'last' is a special keyword that specifies that it should replace the last callout generated by this trigger
// This behaves identically to the previous example.
sequence { e1, s ->
    callout {
        tts "Foo"
        text "${tts} ${->e1.estimatedRemainingDuration}"
    }
    waitMs 2000
    callout {
        tts "Bar"
        text "${tts} ${->e1.estimatedRemainingDuration}"
        replaces last
    }
}
// Finally, you can use the 'forceExpire()' method to remove a callout from the screen
sequence { e1, s ->
    def theCall = callout {
        tts "Foo"
        text "${tts} ${->e1.estimatedRemainingDuration}"
    }
    waitMs 2000
    theCall.forceExpire()
}
```


By default, the 'block' [concurrency mode](../docs/Sequential-Triggers.md#concurrency-mode) is used. You can change it by adding
`concurrency block`, `concurrency replace`, or `concurrency concurrent`.

# Injecting Your Own Globals

You can write to the global namespace by using `globals.varName = value`. This allows you to do things like 
[Custom Search Filters](https://github.com/xpdota/event-trigger/wiki/Groovy-Examples#custom-search-filters) or
[Hybrid Easy/Scripted Triggers](../Automarkers.md#hybrid-easy-triggerscripted).