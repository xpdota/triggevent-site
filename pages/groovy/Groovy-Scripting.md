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

## Triggers

You can now do full-blown triggers from within a Groovy script.

Important Note: The script will do nothing until it is run. After pasting the script,
you can run it and it will work until you close the program. To make it persistent, save the script and check the
"run on startup" box.

Here is an example of a simple (non-sequential) trigger:

```groovy
groovyTriggers.add {
	named "foo" 
	type AbilityCastStart 
	when { log.info "foo" ; it.abilityIdMatches(0x5EF8) } 
	then { event, context -> 
		context.accept(new TtsRequest(event.ability.name)) 
		log.info "bar"
	}
}
```

This will call out the name of the ability when you start casing Dosis III.

```groovy
groovyTriggers.add {
	named "bar"
	type AbilityUsedEvent
	when { it.abilityIdMatches(0x5EFB) }
	sequence { e1, s -> 
		s.accept(new TtsRequest("foo"))
		s.waitMs(1_000)
		s.accept(new TtsRequest("bar"))
//		s.accept(new SpecificAutoMarkRequest(e1.source, MarkerSign.IGNORE1))
	}
}
```

This will call out "foo" when you use Dyskrasia II, wait one second, then call out "bar".

## Injecting Your Own Globals

You can write to the global namespace by using `globals.varName = value`. This allows you to do things like 
[Custom Search Filters](https://github.com/xpdota/event-trigger/wiki/Groovy-Examples#custom-search-filters) or
[Hybrid Easy/Scripted Triggers](../Automarkers.md#hybrid-easy-triggerscripted).