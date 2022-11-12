---
title: "[UE5.1] Advanced Locomotion (Community) to Metahuman"
date: 2022-11-12T14:55:30-04:00
categories:
  - UE5
tags:
  - Tutorial
  - Blueprints
toc: true
toc_label: "Table of Contents"
toc_sticky: true
toc_icon: "gamepad"  # corresponding Font Awesome icon name (without fa prefix)
excerpt: "Let's learn about using a Metahuman with Advanced Locomotion (Community)."
header:
  overlay_image: /assets/images/tutorials/alsv4/e001.png
  teaser: /assets/images/tutorials/alsv4/e001.png
---

**Note:** This guide is only applicable to Unreal Engine 5.1.
{: .notice--success}

Before we begin, please ensure you have downloaded ALS-Community and enabled the plugin in your project. You can download it and learn how to install here: [GitHub - dyanikoglu/ALS-Community: Replicated and optimized community version of Advanced Locomotion System V4 for Unreal Engine 5.0 with additional features & bug fixes](https://github.com/dyanikoglu/ALS-Community)

Please also make sure you have a MetaHuman imported into your project and it is setup ready for use. To learn how to do this, please check here: [Exporting to Unreal Engine 5](https://docs.metahuman.unrealengine.com/en-US/exporting-metahumans-to-unreal-engine-5/)

This guide is only applicable to MetaHumans imported using the above method.

Unreal Engine 5 has changed how you can share animations between your characters. In some situations, this can actually produce more accurate results than what was achievable within Unreal Engine 4.

Advanced Locomotion (both the Community C++ version and the Marketplace original) has always been a large animation system relying on a number of different systems at runtime to craft a well-respected runtime animation system.

Due to the size and complexity of Advanced Locomotion as a whole, for this guide, we are going to step away from the idea of retargeting the animations of the system and instead focus on utilising the end results of the system to drive our MetaHuman.

It should be noted that this is not the most performant method but what you might lose in performance, you will get back in ease of use. 

Your mileage may vary but this method was used in the first of the KxF Micro-Games Series (more information on that at a future date) and whilst no formal performance comparison has taken place; the end result did not affect the performance of that project in a noticeable way (again, your mileage may vary).

***

At this point, we are ready to begin. Inside your project, you should have your MetaHuman and you should have ALS-Community installed and enabled.

The secret sauce behind the approach we’ll be taking is the IK Retargeter. The basic idea behind what we’re about to do is: We’ll reinterpret the location of key bones during the ALS animation (at runtime) and emulate the positions on our metahuman.

To make IK Retargeting work, you need IK Rigs. These can be tedious to set up for every mesh you need them to work for but luckily the Metahuman already comes with one and I can supply you with an IK Rig for your ALS character.

You can download the IK Rig for the ALS Character here: [CLICK ME](https://github.com/KITATUS/KFRetarget/raw/main/IKR_ALS.uasset)

**Note:** This download will only work in Unreal Engine 5.1.
{: .notice--success}

With your project CLOSED, place this file at this location:

"Plugins/ALSV4_CPP/Content/AdvancedLocomotionV4/CharacterAssets/MannequinSkeleton"

# Creating The IK Retargeter
To create the IK Retargeter, go to your Content Browser and go to Add > Animation > IK Rig >  IK Retargeter.

[![styled-image](/assets/images/tutorials/alsv4/e002.png "A screenshot of how to find the IK Retargeter"){: .align-center style="width: 100%;"}](/assets/images/tutorials/alsv4/ed002.png)
A screenshot of how to find the IK Retargeter
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}

In the “Pick IK Rig To Copy Animation From”, pick “IKR_ALS”. Name it “IKR_ALS_MH” (ALS > MetaHuman).

Double-click the created asset.

Under “Target” in the details panel, set the “Target IKRig Asset” to “IK_metahuman” (located at “Content/MetaHumans/Common/Common”)

[![styled-image](/assets/images/tutorials/alsv4/e003.png "Target IK Rig"){: .align-center style="width: 100%;"}](/assets/images/tutorials/alsv4/ed003.png)
Target IK Rig
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}

You can then go to the Asset Browser (within the IKR_ALS_MH) window to confirm the real time retargeting is going to work.

You may notice that the fingers in preview animations are quite warped. This is due to an incorrect auto resolve within the Chain Mapping.

# Fixing The Fingers
Head into the “Chain Mapping” tab and find every “Metacarpal” entry (such as LeftPinkMetacarpal .etc). Set the “Source Chain” of these entries to “None”.

[![styled-image](/assets/images/tutorials/alsv4/e004.png "Removing Metacarpal References"){: .align-center style="width: 100%;"}](/assets/images/tutorials/alsv4/ed004.png)
Removing Metacarpal References
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}

Doing this should fix your retargeting issues.

We are now ready to use this IK Retargeter at runtime.

# Setup For IK Retargeter
Duplicate ALS_CharacterBP from “Plugins/AdvancedLocomotionSystemCommunityContent/AdvancedLocomotionV4/Blueprints/CharacterLogic” and name it “BP_MainChar”.

Open your Metahuman Blueprint (“Content > MetaHumans > METAHUMANNAME > BP_METAHUMANNAME”). 

Copy the Body, Face, Torso, Legs, Feet .etc to your BP_MainChar. Try and copy the same hierarchy and make it a child of the “Mesh”.

[![styled-image](/assets/images/tutorials/alsv4/e005.png "Correct Hierarchy"){: .align-center style="width: 100%;"}](/assets/images/tutorials/alsv4/ed005.png)
Correct Hierarchy
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}

Ensure the Location, Rotations and Scales are set up correctly (as per your Metahuman Blueprint).

# Animation Blueprint
Create a new Animation Blueprint (Add > Animation > Animation Blueprint). Use “metahuman_base_skel” as the skeleton.

[![styled-image](/assets/images/tutorials/alsv4/e006.png "Animation Blueprint Creation"){: .align-center style="width: 100%;"}](/assets/images/tutorials/alsv4/ed006.png)
Animation Blueprint Creation
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}

Open the created asset and head to the Event Graph. Add an “Event Blueprint Initiliaze Animation” node. Grab the output from “Try Get Pawn Owner” and cast it to your “BP_MainChar”. Get “Mesh” from the output pin and promote that to a variable called “Parent Mesh”.

[![styled-image](/assets/images/tutorials/alsv4/e007.png "AnimBP Event Graph"){: .align-center style="width: 100%;"}](/assets/images/tutorials/alsv4/ed007.png)
AnimBP Event Graph
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}

Now head to the Anim Graph.

Create a “Retarget Pose From Mesh” node and connect to the “Result” of the Output Pose. Bring in your “Parent Mesh” variable and connect it to the “Source Mesh Component” pin.

[![styled-image](/assets/images/tutorials/alsv4/e008.png "AnimBP Anim Graph"){: .align-center style="width: 100%;"}](/assets/images/tutorials/alsv4/ed008.png)
AnimBP Anim Graph
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}

Select the created “Retarget Pose From Mesh” node and set the “IKRetargeter Asset” to the one we created earlier (“IKR_ALS_MH”). Compile, save and close.

[![styled-image](/assets/images/tutorials/alsv4/e009.png "Retarget Pose From Mesh Details Panel"){: .align-center style="width: 100%;"}](/assets/images/tutorials/alsv4/ed009.png)
Retarget Pose From Mesh Details Panel
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}

# Set Leader Pose Component
Head back to your BP_MainChar. Select the “Body” mesh and set the Animation Mode to to “Use Animation Blueprint” and use the Animation Blueprint we created.

Head to the Construction Script. Grab the “Body” variable (from the Components tab) and from the output pin, create a “Set Leader Pose Component” node. Make sure the ”Body” is connected to the “New Leader Bone Component” pin.

Now bring in all your other Metahuman meshes (Face, Head .etc) and connect them to the “Target” pin of the Set Leader Pose Component node. Ensure the node is connected to the output pin of your Construction Script function input.

[![styled-image](/assets/images/tutorials/alsv4/e010.png "BP_MainChar Construction Script"){: .align-center style="width: 100%;"}](/assets/images/tutorials/alsv4/e010.png)
BP_MainChar Construction Script
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}

# Hide Advanced Locomotion Mesh
Select “Mesh” and set “Cast Shadow” to false.

If we set the “Mesh” to “Hidden in-game” then this means our Metahuman won’t work. Not sure if this will get fixed in the future or if it is an intentional feature. 

To get around this, we can create a new Material. Call it “M_Invis”. Set the blend mode to “Translucent”, the shading model to “Unlit” and set Cast Raytraced Shadows to false.

[![styled-image](/assets/images/tutorials/alsv4/e011.png "M_Invis Details Panel"){: .align-center style="width: 100%;"}](/assets/images/tutorials/alsv4/e011.png)
M_Invis Details Panel
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}

Now create a “Constant”, set it to 0 and connect to the Opacity. Compile and Save and head back to the BP_MainChar.

[![styled-image](/assets/images/tutorials/alsv4/e012.png "M_Invis"){: .align-center style="width: 100%;"}](/assets/images/tutorials/alsv4/e012.png)
M_Invis
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}

Select the “Mesh” again and set all of the materials to the “M_Invis” material we created.

And we’re done! Your Metahuman will now use the ALS system to drive the animation.

## Extras
### Video
{% include video id="RT8TqGrN13g" provider="youtube" %}

### Project Files
The project files for this project (Unreal Engine 5.1) are available here: [PROJECT FILES](https://github.com/KITATUS/KFRetarget)