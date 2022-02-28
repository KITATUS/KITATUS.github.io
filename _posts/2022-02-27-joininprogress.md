---
title: "[UE4/UE5] Handling Spawning Co-op Partners in Multiplayer"
date: 2022-02-27T14:55:30-04:00
categories:
  - UE4
  - UE5
  - Blueprints
  - C++
  - Networking
tags:
  - Tutorial
  - Blueprints
  - C++
  - Networking
toc: true
toc_label: "Table of Contents"
toc_sticky: true
toc_icon: "gamepad"  # corresponding Font Awesome icon name (without fa prefix)
excerpt: "A breakdown of how to spawn a friendly player when they join a session in progress in a co-operative Unreal Engine game."
header:
  overlay_image: /assets/images/tutorials/joinInProgress/ed_001.jpg
  teaser: /assets/images/tutorials/joinInProgress/ed_001.jpg
---

**Note:** The screenshots used in this guide are from Unreal Engine 5 (Early Access) but everything here is compatible with most previous and future versions of Unreal Engine (4 / 5 / beyond).
{: .notice--success}

Today, we are going to look at how we can deal with spawning friendly players in a co-operative game. First, we will look at the pseudo-code and then how we can achieve said functionality in Blueprints and then C++. 

One thing you have to keep in mind when spawning players in a co-operative game is that they might join after the game has already started, so we want a system that doesn't judge if a player is right then when the game begins or if they join afterwards.

[![styled-image](/assets/images/tutorials/joinInProgress/jip_001.jpg "An image of Example01"){: .align-center style="width: 100%;"}](/assets/images/tutorials/joinInProgress/jip_002.jpg)
An image of Example01.
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}

We are going to leverage the Game Mode class for this, as the Game Mode handles important things such as when players join the game, the numbers of player required to play .etc and is not replicated to remote clients (it only exists on the server). As we are dealing with players joining the game, this is definately the right place.

## Psuedo-Code
The basic idea behind the code we're going to need is as so:

Within the game mode; when a player joins, check if they have a pawn. If they don't, they haven't already been spawned. So let's find the first player we can spawn near them. If we don't find one, let's find a player start placed in the world somewhere and use the transform for that instead. By now, we have a spawn transform to use, so lets use that and spawn the new player character. Spawn the player with a random offset on the X and Y.Once the character has been spawned, tell the new controller to possess it and then we should fire off an intialize on it.

<iframe frameborder="0" style="width:100%;height:451px;" src="https://viewer.diagrams.net/?highlight=0000ff&nav=1&title=PlayerJoinFlow.drawio#R7Vvbdto4FP0aHtvlC3bgkWuaNs1qVzKT5GmWsIXtIkuuLAL06yvZ8t2AEwIkGecl1tGRLOls7XMBOvrIX19SELjfiQ1RR1PsdUcfdzRNVQyd%2FxOSTSwxdSMWONSzpVImuPX%2BwGSklC49G4YFRUYIYl5QFFoEY2ixggxQSlZFtTlBxbcGwIEVwa0FUFV679nMlbswlEz%2BBXqOK99sKLJjBqyFQ8kSy9d1NF3r6abai7t9kEwl9UMX2GSVE%2BmTjj6ihLD4yV%2BPIBJHm5za%2FdXmHl0vzMuvP8Pf4J%2Fht7ubfz%2FFk02fMyTdIIWYve7UWjz1E0BLmJxCtFe2SY43OiEoJlE6%2BtBlPuKPKn%2F8BRnbSDiAJSNcRChziUMwQNeEBFJvTjCTaqpoQ2wPhNl5e4aItYhFUw8h%2BQ7ekvo93goZJYvUsmKC1BBCGYEZRMPUlCOCCOVdmGAoprI5VORessVNMqmcPhnGMTAeD1RjItedk08n08FEvLGhNaTVQrKkFtyhJ28fA9SBu%2BYzYz2xoxzupa0vIfEhoxuuQCECzHsqXg4g75iT6mVI4Q8SLM8Ajl4Bzg8ENpBy2Vfi4XAHjIQJV67H4G0AopNZcV4qQisPGb7HoYNAGEqD78HDnOMoZ7XhyBxpxgns%2FAQpg%2BudlpG9puQTSbcJvawy7lITmZvjra5yuCn7V7%2BwAmffyALT4JP%2Bn7bYmClQM1vtvzVFUqi99jX0kLMwXHvsIff8KKb6bMjWeJ1QgWhsUl7g233IN3KjRDMbFrWScVsoIuP7l6OjdBtfkRbMhrRwcSIWqIVO95zQyeDymOvZB50MLY8FsHw06BwACTn0B%2BdxltFWSkoJb%2FX7n%2Fv5v%2BKEMXDlHCWYpYt6uf8xK%2F5nTMR1cSHfggl84VDwLAyi4zYRP9bhjDsn0xFPLuCnwJda7QnACoseRCEQ8fC06sdc4s%2BW4X4fVvRKHApT4HtIHN4XiJ4g8yxQ4%2BkA8hzMGxaHAvemte6Ov9LDDm%2BZWesuujz8Qr5DD6iqDV2g%2Bho%2BcDuLVuF0Q5grjroNZraZUtNLtjTPHs7orVN6g05JTUoDuYCm3n7aOfOaZJk5JriEwgtO1l7IIi5Q0kznjgIczgn1W4LYShC6WgobeucmCKVi4UdYzVf59ljRTBSG3h8wixTEiQkSCKXZ6jy3OCPu5NFAdviebUdkE4igKNqVMewY45IZZeWiaEEpLNLUblNWTNTUultNqfb2c323xpLasSxpnpPp28x1i1WMKtHvoNpzpa4JLb2nMOH%2FCZ5avf6B2ImGDigFm5yCJObGKXDXLJXcy%2Fr9A%2FU1pYTzeMUvzaR3nXjOHY6AyIFXIkWee9gWB4SrqTLMAqJABkRbsmkmUikniqJqUu4w0cqCqbLKXMCwTcWP4dNLiNaVqkvXlGOl4rV4rKbiN6SNzhpEZ6VMXKuJzvRTRme9Ns5%2B4Z0sX0rjzHG2%2Bobq%2FGl49KySivoRY6V%2Bw1jpZBWVXausFFTiKsotXz37kKWU1%2BeFcimlW%2BOsT1xrrVbLWmfdyFl3S6bsntlZqy%2BppVhL%2BpQeYfGehgvILPdw8j9FlWWb8Xfc%2FC1ccWzGj5m8CeUb56T8ZJk5WhjYIo98iHJJ8fQYdVOPr4bglvO3c75SigVriEKtCwaPx%2Fm9t8kUpwgT3xFTNC2kqWf9ApFarT7dynpQ%2BikbYFH5CEMKGBTckWhcE6ulj9300S19ZKMn3%2B3eRx%2FGsegj%2FTZ1Sx9vmT4umtLHoYX4w%2BijWiy8k8OwRfy4Om3xU6cEoYhMolp0QMIQ8kuuKRiuUqoZuYACSyQcLZ805ROtpjR1Wj5Rq%2FWFyRpaSyY%2Bvuho5u%2Bl%2BH3G8Aozj6eV3CypiPeT6IMHF7ZIOBgJ6sXxkMCb2c9s4s%2B9sp8y6ZO%2F"></iframe>

The reason behind the Initialize event would be to do everything that you'd normally do in the BeginPlay (such as locally setting the camera for a player) as it might not have been ready yet as the player didn't immediately possess it.

## Blueprint
Let's first look at how we would implement this in Blueprint. Inside the GameMode blueprint, we have hooked into the function for  ```HandleStartingNewPlayer``` by adding the Blueprint exposed event for it. From where, we pass into a custom function;  ```SpawnMPChar``` , which takes in the PlayerController output from   ```HandleStartingNewPlayer```.

[![styled-image](/assets/images/tutorials/joinInProgress/jip_002.jpg "The Blueprint graph for GM_Example01"){: .align-center style="width: 100%;"}](/assets/images/tutorials/joinInProgress/jip_002.jpg)
The Blueprint graph for GM_Example01.
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}

Inside  ```SpawnMPChar``` we first check if this player already has a pawn. In the use case of this example, either they have the character ready or they don't but in your use case, you might want to further check if the character they have is the character you want them to have or if some variables need to be setup on said character, you could use this to confirm that character is in a state you want it to be.

**Note:** Did you know that you can reference inputs from functions in Blueprint without having to have wires everywhere? Once the function has been created, compile and save then when you open the "All actions for this Blueprint Window" (usually by right clicking), you can find and use the variable!
{: .notice--info}

If the player controller doesn't have an accociated character, we need to create them one.

[![styled-image](/assets/images/tutorials/joinInProgress/jip_003.jpg "The start of SpawnMPChar"){: .align-center style="width: 100%;"}](/assets/images/tutorials/joinInProgress/jip_003.jpg)
The start of SpawnMPChar.
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}

We clear our the SpawnTransform to ensure we're not accidently overwriting any existing data (normally this won't be the case but it doesn't hurt to be careful!) then we try and find an existing character in the scene (to spawn beside). We grab the first value that comes back from the  ```GetAllActorsOfClass```  node and check to see if it returned valid or if we didn't find anything.

**Note:** If you were dealing with a game where you only wanted people to spawn at a player location if certain criteria were met; such as location they were at or what team they're on - this would be the perfect time to do it.
{: .notice--info}

[![styled-image](/assets/images/tutorials/joinInProgress/jip_004.jpg "How we are looking for the player within the example"){: .align-center style="width: 100%;"}](/assets/images/tutorials/joinInProgress/jip_004.jpg)
How we are looking for the player within the example.
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}

If we actually found a player, we're going to get their transform and use it. If we didn't find a player (i.e the temporarily stored variable is null), we'll instead look for a PlayerStart and use the transform of that.

[![styled-image](/assets/images/tutorials/joinInProgress/jip_005.jpg "Getting the SpawnTransform"){: .align-center style="width: 100%;"}](/assets/images/tutorials/joinInProgress/jip_005.jpg)
Getting the SpawnTransform.
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}

Once we have a SpawnTransform, we break it apart to add some random variation. This is so if we have multiple people spawning, the chances of them spawning the exact same spot are significantly lower.

[![styled-image](/assets/images/tutorials/joinInProgress/jip_006.jpg "Adding random X and Y alterations to the SpawnTransform"){: .align-center style="width: 100%;"}](/assets/images/tutorials/joinInProgress/jip_006.jpg)
Adding random X and Y alterations to the SpawnTransform.
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}

Once we have set the SpawnTransform to a more appropriate value, we then simply spawn the Character and tell the PlayerController (variable input of this function) to possess that created character.

[![styled-image](/assets/images/tutorials/joinInProgress/jip_007.jpg "Spawning and possessing the Character"){: .align-center style="width: 100%;"}](/assets/images/tutorials/joinInProgress/jip_007.jpg)
Spawning and possessing the Character.
{: style="text-align: center; font-size:0.7em; font-style: italic; color: grey;"}

...And that's all there is to it! If we open this map using ``` Open Map_Example01?listen``` and we join the match once it has started, you can see that we spawn by the player and are ready to go on a co-operative adventure with them!

As previously explained, if you have custom code that needs to run locally on a client on Begin Play, it's probably a good idea to create a "Initialize" function and do it there instead - calling it after the Possess. Alternatively, you could also use the OnPossessed event within the Character Blueprint.

Now, let's see how we would approach this in C++.

## C++
Inside the GameMode class, ```GM_Example02.h``` overrides the ``` HandleStartingNewPlayer ``` similar to how we did in Blueprint. The main difference in C++ is the fact that ```HandleStartingNewPlayer``` is a BlueprintNativeEvent, meaning that if we wish to override it in C++, we actually have to override the "Implementation" of it by using ```Class_Implementation```.

<script src="https://gist.github.com/KITATUS/47d4afb09ab695d5d9a9b937d56b90f2.js"></script>

With ``HandleStartingNewPlayer``` overriden in the .h, let's look at the implementation itself.

<script src="https://gist.github.com/KITATUS/31c073a372a367743fd79d79b9850334.js"></script>

If we dive into the function; you'll notice that we do pretty much everything we did in the Blueprint, with the addition of a few minor C++ things in addition to one key change to before.

<script src="https://gist.github.com/KITATUS/79ebc1dda97b6937af932a4c21eb063b.js"></script>

First up, we're checking if the player coming in already has a pawn, specifically (this time) if they have an Example02Char. If they do, we don't need to do anything but if they don't we now check to see if they have a pawn that isn't Example02Char. If they do, we don't want that so let's destroy it.

Now we've got a blank slate to spawn them an Example02 character. From here, we try and find any characters that already exist in the map. If we find on, we use their Transform and if not, we find the PlayerStart and use the transform for that.

<script src="https://gist.github.com/KITATUS/96e5c6378fa7e6ca40dfc7f20387fcc2.js"></script>

With SpawnTransform in hand, we apply our X and Y variations just like we did in the Blueprint.

<script src="https://gist.github.com/KITATUS/25ed125022212498ebf0b60e77db1e22.js"></script>

Finally, we spawn the player and tell the controller to possess it - just like before.

<script src="https://gist.github.com/KITATUS/7575331b8264d745f455e54fda63fa8d.js"></script>

...And that's it! The player will now spawn at a PlayerStart if they're not Example02 character (and there's no Example02 character in the scene) or it will spawn alongside their co-operative buddy.

## Extras

### Project Files
The project files for this project (Unreal Engine 5.0 EA) are available here: [PROJECT FILES](https://github.com/KITATUS/KFSpawnInProgress)