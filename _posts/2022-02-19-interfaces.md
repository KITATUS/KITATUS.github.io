---
title: "[UE4/UE5] Interfaces (BP + C++)"
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
excerpt: "Some examples of Interfaces in Unreal Engine and when you could use them."
header:
  overlay_image: /assets/images/tutorials/interfaces/interface_001.jpg
  teaser: /assets/images/tutorials/interfaces/interface_001.jpg
---

**Note:** The screenshots used in this guide are from Unreal Engine 5 (Early Access) but everything here is compatible with most previous and future versions of Unreal Engine (4 / 5 / beyond).
{: .notice--success}

Interfaces are a very powerful tool you can use in Unreal Engine when you need to interface between actors and don't need to specifically know too much about the other actor. 

The general idea behind an Interface is to share functionality between different classes that aren't too similar to each other; such as objects that could be set on fire - just because you could set wood and grass on fire doesn't mean you should treat your grass actor like a tree.

We will look at two examples; one for a Blueprint Interfaces and one for a C++ created Interface.

## Examples
### Example #1 - Fire (Blueprint)
For the first example, we are displaying a basic understanding of Blueprint Interfaces by allowing different objects to be burnt in specific types of fire.

[![styled-image](/assets/images/tutorials/interfaces/interface_002.jpg "A screenshot of Example 01"){: .align-center style="width: 100%;"}](/assets/images/tutorials/interfaces/interface_002.jpg)
A screenshot of Example 01
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}

There are three times that the user can place in either the red flame or the blue flame. They react as follows:
* Wood - Red fire only
* Stone - Blue fire only
* Chair - Both Red and Blue fire

The way this system works is via the two Interfaces created; ``` BPI_Flammable_Red ``` and ``` BPI_Flammable_Blue ```. These BPIs have idential functions inside (a simple function with no variable inputs named ``` FlameOn_Red ``` and ``` FlameOn_Blue ``` respectively) but are distinctly seperate Interfaces.

The main reason these are seperate interfaces are to show off one of the key features of an Interface; giving the ability for an object to communicate with one of three items without having to know specfically which actor it is or anything outside of "I can do this thing you want me to do".

If we take a look at either BP_Wood or BP_Stone, we can see that simply have the incoming event from the Blueprint Interface and a function to activate the attached particle system.

[![styled-image](/assets/images/tutorials/interfaces/interface_003.jpg "The Interface Event is generated from the function we made in the Blueprint Interface"){: .align-center style="width: 100%;"}](/assets/images/tutorials/interfaces/interface_003.jpg)
The Interface Event is generated from the function we made in the Blueprint Interface.
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}

If we take a peak into BP_Chair, we can see that it has both Interfaces, therefore we can tell it to do specific things based on which Interface has been triggered.

[![styled-image](/assets/images/tutorials/interfaces/interface_004.jpg "In order to make sure we don't have two fires happening at once, we've introduced a simple bool to make sure the chair can only be on fire once"){: .align-center style="width: 100%;"}](/assets/images/tutorials/interfaces/interface_004.jpg)
In order to make sure we don't have two fires happening at once, we've introduced a simple bool to make sure the chair can only be on fire once.
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}

To check, add or remove an attached interface on a Blueprint - you can head over to the Class Settings -> Interfaces to view/alter the Implemented Interfaces.

[![styled-image](/assets/images/tutorials/interfaces/interface_005.jpg "BP_Chair implements both of our Blueprint Interfaces"){: .align-center style="width: 100%;"}](/assets/images/tutorials/interfaces/interface_005.jpg)
BP_Chair implements both of our Blueprint Interfaces.
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}

Both fire Blueprints use the same class, they switch functionality on the exposed bool ``` bIsBlueFire ``` so to see what is going on, we can open either of them.

[![styled-image](/assets/images/tutorials/interfaces/interface_006.jpg "The Blueprint Graph for BP_Fire"){: .align-center style="width: 100%;"}](/assets/images/tutorials/interfaces/interface_005.jpg)
The Blueprint Graph for BP_Fire.
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}

When an actor overlaps with BP_Fire, we check to see if this fire is supposed to be a Blue fire or a Red fire. Depending on the choice, we check to see if the actor overlapping has the interface to burn (Blue interface for blue fire .etc). If the actor does, we fire the event to let them know they have touched fire. 

It is important to note that BP_Fire has no knowledge of the Wood, Chair or Stone. Even if we added another class, it still has zero knowledge of them. That is because all it cares about is "Whoever this is, can they run this function? If so, tell them to do it".

Before we move onto the C++ example, it is important to mention that an alternative implementation you could have done in this situation is to use a shared base class (where Wood, Chair and Stone shared a common base class with the required function in) but as you have seen, adding an Interface allows us to add this kind of functionality to any actor, mixing and matching different interfaces without having to have unused code in your classes that might not utlize the bits we don't want.

### Example #2 - Interact (C++)
Within Example 2, we are showcasting Interfaces with a simple Interact system. The basic flow is that when a player gets close to either of the two objects in the scene (BP_Chest or BP_Interact_Text), the player is notified that they can interact. When the player presses the interact button, we check with the stored actor and send off our interaction request.

[![styled-image](/assets/images/tutorials/interfaces/interface_007.jpg "A screenshot of Example 02"){: .align-center style="width: 100%;"}](/assets/images/tutorials/interfaces/interface_007.jpg)
A screenshot of Example 02.
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}

First let's take a look at the .h file (KFI_Interact.h). It is important to note that for this interface, there is no accompanying .cpp file as we have used the ```BlueprintNativeEvent``` flag to enable us to call these functions from both Blueprint and C++.

<script src="https://gist.github.com/KITATUS/0a810ff8ebe38f762d282ee739bf5812.js"></script>

There are two classes here, one is a UInterface (```UKFI_Interact```) and one is named ``` IKFI_Interact ```. The idea here is that the UInterface deals with the internal logic (and does not need to be changed) and the ```IKFI_Interact``` deals with what you've come to expect from your classes; the outward facing functions.

There are three functions here ready to be implemented by those who inherit this Interface:

<script src="https://gist.github.com/KITATUS/a5e3d0d8081d4228591ade80c5cfd3ea.js"></script>

You can see how these are implemented in C++ by looking at KF_Interact_Text.h.

<script src="https://gist.github.com/KITATUS/a2798a59021b17e8942fc9f545c181bb.js"></script>

Just under the ```UCLASS()```, the Interface gets added on the line ``` class KFINTERFACES_API AKF_Interact_Text : public AActor, public IKFI_Interact```. 

Near the bottom, we can see the implemented function for ```InteractRequest``` on the line: ```virtual void InteractRequest_Implementation(AActor* InteractableActor) override;```. The ```_Implementation``` part is due to the fact we made our originating function a ```BlueprintNativeEvent```.

Im KF_Interact_Text.cpp we have an example of using the interface in C++ to communicate to a Blueprint:

<script src="https://gist.github.com/KITATUS/0cfde4c3a7fae78ed1f463e56d1e5d9a.js"></script>

Specifically in the ```OverlapStarted``` and ```OverlapEnded``` functions, you can see that we check if the OtherActor implements the interface we need and if they do, tell them to fire their version of the function and perform the visual feedback on our end afterwards.

<script src="https://gist.github.com/KITATUS/70f611ba2cde78f08d8b9106b613e1e8.js"></script>

As we have implemented our Interface on the Blueprint side for the character, if we did ``` ICoolInterface* TempCool = Cast<ICoolInterface>(TempActor)```, it will always return nullptr. Where possible, it is always preferred to seek out the "Unreal" way, especially when dealing with support for Unreal systems such as Blueprint. In this case, we would use ```TempActor->Implements<UCoolInterface>```. Notice that in that example we talk to the ```U``` version of Interface and not the ```I```. It is good practice to standardize your Interface calling so it is highly recommeneded to use the ```Actor->Implements``` approach.

To see a Blueprint version of dealing with this C++ created Interface, we can check ```BP_Chest```.

[![styled-image](/assets/images/tutorials/interfaces/interface_008.jpg "A screenshot of BP_Chest"){: .align-center style="width: 100%;"}](/assets/images/tutorials/interfaces/interface_008.jpg)
A screenshot of BP_Chest.
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}

Within ```BP_Chest```, when our sphere has been overlapped (or that overlap has ended), we trigger the C++ created Interface functions for Entering and Leaving the interaction zone. You can see what this code triggers by checking ```BP_FirstPerson_Example02```.

[![styled-image](/assets/images/tutorials/interfaces/interface_009.jpg "A screenshot of BP_FirstPerson_Example02"){: .align-center style="width: 100%;"}](/assets/images/tutorials/interfaces/interface_009.jpg)
The Interact logic as appears in BP_FirstPerson_Example02.
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}

When EnteredInteractionZone has been triggered, you can see that we are storing the actor and creating the interaction widget. Then if we peek at when LeftInteractionZone is fired off, we check to see if it is our stored actor calling the function. If so, remove the interact widget and clear the ```InteractableActor``` variable. 

Taking a look at the ```InputAction Interact``` function (which is used when the player presses the "Interact" button), we can see that if the ```InteractableActor``` is valid from EnteredInteractionZone then tell that actor to fire off their "InteractRequest" interface function.

Jumping back to BP_Chest, we can see the InteractRequest implementation that if closed, opens and if open, closes the lid of the chest.

## How To Create Your Own
### Creating a Blueprint Interface
A Blueprint Interface is a seperate class to most Blueprint classes. To create one, go to Add in your Content Browser, select Blueprints then Blueprint Interface.

[![styled-image](/assets/images/tutorials/interfaces/interface_005.jpg "How to create a Blueprint Interface"){: .align-center style="width: 100%;"}](/assets/images/tutorials/interfaces/interface_005.jpg)
How to create a Blueprint Interface.
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}

#### Using a Blueprint Interface
Head inside the class you'd like to have the Interface attached to and go to the "Class Settings" (on the top ribbon). Scroll to the "Implemented Interfaces" part then add as required.

[![styled-image](/assets/images/tutorials/interfaces/interface_010.jpg "How to implement a Blueprint Interface on a Blueprint Actor"){: .align-center style="width: 100%;"}](/assets/images/tutorials/interfaces/interface_010.jpg)
How to implement a Blueprint Interface on a Blueprint Actor.
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}

For any functions you'd like to bring in, you can either do it as a function by right clicking the Interface function in the MyBlueprint panel of your Blueprint and selecting  "Implement event".

To call a Blueprint Interface event on an actor, simply drag out anywhere you have that actor reference and select "YOURFUNCTIONNAME (Message)" to trigger the function on that actor.

### Creating a C++ Interface
Create a .h file with TWO classes; one a UINTERFACE(MinimalAPI) named U{CLASSNAMEHERE}, with the parent set to UInterface. This class should only have ```GENERATED_BODY()``` inside. 

<script src="https://gist.github.com/KITATUS/5485b7a382c42b8c70de9705e59f211a.js"></script>

The second class does not need a macro placed on top of it as long as you make sure that it has an identical name to the previous class outside of replacing the U prefix with I (for "Interface"). Here, you can use the ```BlueprintNativeEvent``` flag to expose created functions to both Blueprint and C++ but if you do not want this, remember that non-virtual functions will need default implementations (via a .cpp file).

#### Using a C++ Interface

To use a C++ interface in a class, #include it then add the interface's ```I(NAME)``` to the class, just after public CLASSTYPE.

Example code:
```class ACoolActor : public AActor, public ICoolInterface```

To implement the functions, this depends on how you've defined them in the Interface's .h file. If you've done as this guide has recommended (```BlueprintNativeEvent```), an implementaiton would look like this:

Interface code:
``` UFUNCTION(BlueprintCallable, BlueprintNativeEvent) void CoolFunctionA();```

Actor code:
``` virtual void CoolFunctionA_Implementation() override; ```

To call an Interface in C++ code, there are a few ways to do it that change based upon how you have defined the function in the Interface class. The method that works with most definitions is to use:

<script src="https://gist.github.com/KITATUS/f26d5b6cbcf81fc7ac58b04ec18729a2.js"></script>

## Extras
### Video
VIDEO HERE

### Project Files
The project files for this project (Unreal Engine 5.0 EA) are available here: [PROJECT FILES](https://github.com/KITATUS/KFInterfaces/)