---
title: "Event Dispatchers (Delegates)"
date: 2022-02-18T14:55:30-04:00
categories:
  - ue4
  - ue5
  - tutorial
tags:
  - Tutorial
  - Blueprints
  - C++
toc: true
toc_label: "Table of Contents"
toc_sticky: true
toc_icon: "heart"  # corresponding Font Awesome icon name (without fa prefix)
excerpt: "A look at how Event Dispatches (and Delegates) work in Unreal Engine and how you can use them to handle communication between multiple actors."
header:
  overlay_image: /assets/images/tutorials/eventDispatcher/ed_001.jpg
---

The basic idea behind an Event Dispatcher (Or Delegate in C++ land) is that one thing is shouting something to whoever is listening. 

We will look at three examples of Event Dispatchers.

**Note:** The screenshots used in this guide are from Unreal Engine 5 (Early Access) but everything here is compatible with most previous and future versions of Unreal Engine (4 / 5 and beyond).
{: .notice--success}

## Examples
### Example #1 - Open Door (Blueprint)
In this first example, we have multiple uses of Event Dispatchers. The idea here is when the button is pressed, the door should either open and closed. When the door state has been changed, the two objects beside the door should let us know what state the door is in.

[![styled-image](/assets/images/tutorials/eventDispatcher/ed_001.jpg "A screenshot of Example 01"){: .align-center style="width: 100%;"}](/assets/images/tutorials/eventDispatcher/ed_001.jpg)
A screenshot of Example 01
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}


The button has an Event Dispatcher called “ButtonPressed” and has no knowledge of anybody listening, it will just shout this to anybody listening. This is fired when “Interact” is called from the player character.

### Example #2 - Damage Volume (Blueprint)
fdfd
### Example #3 - Player Collecting a Coin (C++)
Fdfd

## How To Create Your Own
### Creating a Blueprint Event Dispatcher
fdfd

### Creating a C++ Event Dispatcher
fdfd

