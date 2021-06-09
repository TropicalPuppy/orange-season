---
title: BullScript
description: 
published: true
date: 2021-06-09T10:07:34.089Z
tags: 
editor: markdown
dateCreated: 2021-06-09T10:07:34.088Z
---

> This page is still being written. The information here is incomplete.
{.is-warning}

# BullScript Language Definition:

BullScript is the language used by Fantasy Farming: Orange Season to process cutscenes, conversations and map effects on the game. We call each instance of those an "Event".

## Basic File Format

Every BullScript Event consists of a name and a list of instructions. The name is specified with a colon at the start of the line, while every other line after it will be parsed as an instruction.

```ruby
:eventName
  instruction
  instruction
  instruction
```

You can have as many events in a single file as you want. Simply declare a new event name with a colon and the previous event will be closed.

```ruby
:event1
  instruction
  instruction

:event2
  instruction

:event3
  instruction
```

## Ignored Content

Anything that you write before the colon in a file will be ignored by the parser.
You can also write "#EOF" anywhere in the file to stop the parser from reading anything after it.

```ruby
This line will be ignored by the parser.

:firstEvent
  instruction
  instruction

:secondEvent
  instruction
  instruction

#EOF

This line will be also be ignored by the parser.
```

The parser will also ignore any events with empty names, as well as anything written between a =begin and a =end:

```ruby
:firstEvent
  instruction

:
  this line will be ignored
  this one too

:secondEvent
  instruction
  =begin
  this line will also be ignored
  =end
  instruction

:thirdEvent
  instruction

```

## Types of Instructions

### Comments

Comments are similar to ignored content as they don't have any effect in the game, but they are still read by the parser and registered as an instruction internally.
You can use comments to add notes and explanations to your other instructions.

To add a comment, simply start a line with the `#` symbol:

```ruby
:eventName
  instruction
  # This line will be parsed as a comment
  instruction
```

### Dialogue Lines

Dialogue lines are used to make a game character say something. They are the most common kind of instruction across all events in the game. The format is very simple, just type a character name, followed by a quoted message. 

```ruby
:eventName
  # Make the player say something
  Player "I'm saying something."
  # The NPC Benjamin then says something else
  Benjamin "That is very interesting!"
```

Keep in mind that once the first message is displayed on the screen, the event will be paused until the player press a button to dismiss it. Only then will the next instruction be executed.

### Condition Blocks

You can use condition blocks to make a character say different things in different contexts.

```ruby
:eventName
  # The player comments on the rain
  if raining
    Player "Wow the rain is so cold!"

    # Devin comments one thing during summer or another during the rest of the year
    if summer
      Devin "At least it's refreshing."
    else
      Devin "And also wet!"
    end
  end

```

You can combine multiple conditions in a single instruction using the AND and OR operators (they **must** be upper case)

```ruby

:eventName
  if fall OR winter
    Player "It's cold today."
  else
    Player "It's hot today."
  end

```

### Choices

```ruby

:eventName
  Benjamin ("Hey Player, what option do you want?", "Option 1", "Option 2")

  if response is "Option 1"
    Player "I like Option 1."
  else
    Player "I like Option 2."
  end
```

### Waiting

Wait a number of frames before executing the next instruction. 60 frames = 1 second.

```ruby
:eventName
  Devin "Hey guess what."
  wait 60
  Player "What?"
```

```ruby
:eventName
  # Wait for Devin to finish walking before executing the next instruction
  wait for Devin
  Devin "I'm tired now."
```

### Loops

### Async Blocks

```ruby

  async
    # This wait will not block the instructions outside the async block
    wait 120
    # Do something
    instruction
  end async

  Player "First message"
  # This wait will not affect the instructions inside the async block
  wait 60

```

### Labels

### Commands

Everything else is done through custom commands. There's a very large list of them, which will be added here at some point.