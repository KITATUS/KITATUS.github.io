---
title: "Event Dispatchers (Delegates)"
date: 2022-02-18T14:55:30-04:00
categories:
  - UE4
  - UE5
tags:
  - Tutorial
  - Blueprints
  - C++
toc: true
toc_label: "Table of Contents"
toc_sticky: true
toc_icon: "gamepad"  # corresponding Font Awesome icon name (without fa prefix)
excerpt: "A look at how Event Dispatches (and Delegates) work in Unreal Engine and how you can use them to handle communication between multiple actors."
header:
  overlay_image: /assets/images/tutorials/eventDispatcher/ed_001.jpg
  teaser: /assets/images/tutorials/eventDispatcher/ed_001.jpg
---

**Note:** The screenshots used in this guide are from Unreal Engine 5 (Early Access) but everything here is compatible with most previous and future versions of Unreal Engine (4 / 5 / beyond).
{: .notice--success}

The basic idea behind an Event Dispatcher (or Delegate in C++ land) is that one thing is shouting something to whoever is listening. 

We will look at two examples; one for a Blueprint Event Dispatcher and one for a C++ Delegate.

## Examples
### Example #1 - Open Door (Blueprint)
In this first example, we have multiple uses of Event Dispatchers. The idea here is when the button is pressed, the door should either open and closed. When the door state has been changed, the two objects beside the door should let us know what state the door is in.

[![styled-image](/assets/images/tutorials/eventDispatcher/ed_001.jpg "A screenshot of Example 01"){: .align-center style="width: 100%;"}](/assets/images/tutorials/eventDispatcher/ed_001.jpg)
A screenshot of Example 01
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}

The button has an Event Dispatcher called “ButtonPressed” and has no knowledge of anybody listening, it will just shout this to anybody listening. This is fired when “Interact” is called from the player character.

[![styled-image](/assets/images/tutorials/eventDispatcher/ed_002.jpg "When Interact is triggered, execute our Event Dispatcher and play the button press animation"){: .align-center style="width: 100%;"}](/assets/images/tutorials/eventDispatcher/ed_002.jpg)
When Interact is triggered, execute our Event Dispatcher and play the button press animation
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}

Within BP_Door, we're saying at the start "Listen for when the button shouts "Button Pressed!". When that happens, if we're closed, open us and if we're open close us. After that, play the animation to move the door. When the animation has completed, shout to whoever is listening that our state has changed and send along our new state too.

[![styled-image](/assets/images/tutorials/eventDispatcher/ed_003.jpg "The blueprint flow of BP_Door"){: .align-center style="width: 100%;"}](/assets/images/tutorials/eventDispatcher/ed_003.jpg)
The blueprint flow of BP_Door
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}

There are two seperate actors that listen for BP_Door and react differently to showcase how one Event Dispatcher can be used to communicate with many things. 

BP_DoorReport_Text is an actor that waits for the door to shout "My state has been updated!" From here, it uses the information told at this time to decide whether to set the text to "CLOSED" or "OPEN".

[![styled-image](/assets/images/tutorials/eventDispatcher/ed_004.jpg "The blueprint flow of BP_DoorReport_Text"){: .align-center style="width: 100%;"}](/assets/images/tutorials/eventDispatcher/ed_004.jpg)
The blueprint flow of BP_DoorReport_Text
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}

The similar (but visually distinct) BP_DoorReport_Alt listens for the door when the game begins but also creates a dynamic material, so it can change the color when the door shouts about its state. 

[![styled-image](/assets/images/tutorials/eventDispatcher/ed_005.jpg "The blueprint flow of BP_DoorReport_Alt"){: .align-center style="width: 100%;"}](/assets/images/tutorials/eventDispatcher/ed_005.jpg)
The blueprint flow of BP_DoorReport_Alt
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}

This is an example of a one-way flow of Event Dispatchers, where object A tells Object B who in turn communicates with object C and D. Next, we will look at a different use case of Event Dispatchers; using an Event Dispatcher to provide communication between classes who have no dedicated knowledge of each other.

[![styled-image](/assets/images/tutorials/eventDispatcher/ed_006.jpg "Interacting with the button starts a chain of Event Dispatchers to correctly change and show the state of the door"){: .align-center style="width: 100%;"}](/assets/images/tutorials/eventDispatcher/ed_006.jpg)
Interacting with the button starts a chain of Event Dispatchers to correctly change and show the state of the door.
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}

### Example #2 - Fire (Blueprint)
fdfd


In a future tutorial, we will revisit this system using Interfaces to further simplify the communication between all of the actors in play - which would be the "correct" way of dealing with a situation as presented here. For now, this has been a servicable showcase of Event Dispatchers and how instead of waiting for an object to tell us something (as with Example #01), we can use the Event Dispatcher system to communicate with other objects.

Another point of optimzation you could have done in this situation is to use a shared base class but as you have seen, adding an Event Dispatcher allows us to add this kind of functionality to any actor, mixing and matching different event dispatchers without having to have unused code in your classes.
### Example #2 - Player Collecting a Coin (C++)
Fdfd

## How To Create Your Own
### Creating a Blueprint Event Dispatcher
fdfd

### Creating a C++ Event Dispatcher
fdfd

## Extras
### Video
VIDEO HERE

### Project Files
The project files for this project (Unreal Engine 5.0 EA) are available here: https://kitatus.github.io/ue4/ue5/eventdispatchersdelegates/