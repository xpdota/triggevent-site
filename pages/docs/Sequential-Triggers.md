---
layout: default
title: "Sequential Triggers"
permalink: /pages/docs/Sequential-Triggers/
description: Sequential Triggers are one of Triggevent's most powerful features
---

# Sequential Triggers

When making triggers for complex mechanics, it is often necessary to consume multiple events (i.e. types of log lines), sometimes of different types,
sometimes spread out over the duration of the mechanic. You also need to worry about properly cleaning up/resetting state. Overall, this makes
already-complex mechanics even more complicated to write triggers for.

Sequential Triggers fix this problem. A sequence of events can be turned into one single trigger. This makes it easier to develop, and results in
clean, readable code that directly follows from the sequence of mechanics. Sequential triggers don't just execute a sequence of actions - they allow
you to listen for more events within the trigger. If you've used a programming language with the `await` keyword, `waitEvent` and other versions of it
let you do just that. You don't need multiple triggers to collect events, you can just wait for those events within the same trigger.

For example, the entirety of Wrath of the Heavens in DSR can be turned into one trigger that closely follows the actual flow of the mechanic:

[//]: # (@formatter:off)
```java
    @AutoFeed
    private final SequentialTrigger<BaseEvent> thordan2_trio1 = new SequentialTrigger<>(30_000, BaseEvent.class,
            // Start this sequential trigger on the actual Wrath of the Heavens cast
            event -> event instanceof AbilityUsedEvent aue && aue.getAbility().getId() == 0x6B89,
            (e1, s) -> {
                // The first thing that happens is the two tethers
                List<TetherEvent> tethers = s.waitEvents(2, TetherEvent.class, tether -> tether.getId() == 5);
                // next, the blue marker
                HeadMarkerEvent blueMark = s.waitEvent(HeadMarkerEvent.class);
                // Give a different callout depending on whether the player is blue marker, tether, or nothing
                if (blueMark.getTarget().isThePlayer()) {
                    s.accept(thordan2_trio1_blueMark.getModified(blueMark));
                }
                else {
                    Optional<TetherEvent> tetherOnPlayer = tethers.stream()
                            // A neat part of TetherEvent is that we have a method to test if *either* target matches
                            // some condition, so we don't have to worry about whether the player is the first or
                            // second target.
                            .filter(tether -> tether.eitherTargetMatches(XivCombatant::isThePlayer))
                            .findAny();
                    if (tetherOnPlayer.isPresent()) {G
                        s.accept(thordan2_trio1_tether.getModified(tetherOnPlayer.get()));
                    }
                    else {
                        s.accept(thordan2_trio1_neither.getModified());
                    }
                }

                // Green marker
                HeadMarkerEvent greenMark = s.waitEvent(HeadMarkerEvent.class);
                // Only call if green is on the player
                if (greenMark.getTarget().isThePlayer()) {
                    s.accept(thordan2_trio1_greenMark.getModified(greenMark));
                }
                // Call out the 'spread'
                AbilityCastStart spread = s.waitEvent(AbilityCastStart.class, acs -> acs.getAbility().getId() == 0x63CA);
                s.accept(thordan2_trio1_protean.getModified(spread));

                // Now, call out the dynamo, BUT modify the call based on whether the player has lightning or not.
                AbilityCastStart donut = s.waitEvent(AbilityCastStart.class, acs -> acs.getAbility().getId() == 0x62DA);
                if (getBuffs().statusesOnTarget(getState().getPlayer()).stream().anyMatch(buff -> buff.getBuff().getId() == 0xB11)) {
                    s.accept(thordan2_trio1_inLightning.getModified(donut));
                }
                else {
                    s.accept(thordan2_trio1_in.getModified(donut));
                }

            }

    );
```
[//]: # (@formatter:on)

## Sequential Triggers in Groovy Scripts

Groovy triggers support the same functionality. Check out [Groovy Scripting](/pages/groovy/Groovy-Scripting.md#sequential-triggers) for more information.

## Sequential Triggers in Easy Triggers

Easy triggers have limited support for sequential actions as well, though they do not currently have a way to collect more events. 
See [Easy Triggers](/pages/tutorials/Easy-Triggers.md) for more information.

# Parts of a Sequential Trigger

Sequential triggers have a "Start Condition" and a "Body". The start condition is what type of event you want to start on. Many complex mechanics
start with a cast bar that does nothing but start the sequence of mechanics (perhaps it also has a raidwide), so that acts as a perfect start condition.

The body takes two arguments: the start event (the event that triggered the start condition), and a "Sequential Trigger Controller". The latter is
how you access the sequential trigger functionality. It allows you to wait for fixed amounts of time, or wait for more events. There are many methods
on this object that provide functionality for various types of waiting, such as waiting for events until some other condition is fulfilled.

## Templates

In the `SqtTemplates`, there are several useful templates. `SqtTemplates.sq` is the most widely used and should generally be the basis of most triggers.
It automatically resets on wipe and improves upon type safety for the start conditions.

There are some other templates for more specific purposes. `SqtTemplates.multiInvocation` is useful for mechanics used multiple times per fight, where
each instance is different. Instead of taking a single body, it takes an array of bodies. The first body will be executed the first time the trigger
is invoked, the second is executed the second time, and so on.

`SqtTemplates.callWhenDurationIs` starts off of an event with a duration (such as a cast bar or status effect), and produces a callout when the
remaining duration drops below a certain value. This is useful for debuffs that don't need to be handled immediately, or excessively long cast bars.

`SqtTemplates.beginningAndEndingOfCast` takes two callouts, and will trigger the first one when the cast bar begins, and the second one when it ends.
Useful for abilities that function like twister but have an actual cast bar to go off of.

## Concurrency Mode

Sequentials can execute in one of three modes:

- Block (the default): While the trigger is "active" (i.e. it has started, but has not finished), the trigger cannot be activated again. 
- Replace: If the trigger hits its start condition while it is active, the active instance is killed and a new instance begins.
- Concurrent: The trigger can execute in parallel. Hitting the start condition will start a new instance regardless of how many instances are already active.

To set the concurrency mode of a sequential trigger, simply call the `setConcurrency` method on the `SequentialTrigger` object.

To do so in a Groovy trigger, write `concurrency block`, `concurrency replace`, or `concurrency concurrent` in the trigger definition (at the same level
as the `name`, not within the body).

For an easy trigger, use the "Concurrency" dropdown.