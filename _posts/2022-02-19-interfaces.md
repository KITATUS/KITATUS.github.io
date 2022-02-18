---
title: "Event Dispatchers (Delegates)"
date: 2022-02-19T14:55:30-04:00
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
excerpt: "Some examples of Interfaces in Unreal Engine and when you should use them"
header:
  overlay_image: /assets/images/tutorials/eventDispatcher/ed_001.jpg
  teaser: /assets/images/tutorials/eventDispatcher/ed_001.jpg
---

**Note:** The screenshots used in this guide are from Unreal Engine 5 (Early Access) but everything here is compatible with most previous and future versions of Unreal Engine (4 / 5 / beyond).
{: .notice--success}

Interfaces are a very powerful tool you can use in Unreal Engine when you need to interface between actors and don't need to specifically know too much about the other actor.

The basic idea behind an Event Dispatcher (or Delegate in C++ land) is that one thing is shouting something to whoever is listening. 

We will look at two examples; one for a Blueprint Event Dispatcher and one for a C++ Delegate.

## Examples
### Example #1 - Fire (Blueprint)

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