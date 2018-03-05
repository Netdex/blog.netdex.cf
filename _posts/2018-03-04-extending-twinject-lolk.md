---
layout: post
title: Supporting Legacy of Lunatic Kingdom w/ Twinject
keywords: touhou, twinject
description: Fun
---
# Twinject?
_twinject_ (**t**ouhou **w**indows **inject**or) is an automated player for the bullet hell games from the *Touhou Project*. Instead of using computer vision techniques to extract data from the game, a DLL is injected and the relevant game data is directly extracted through trampolined functions.

For the unaware, bullet hells are a category of *Shoot 'em Up* video games where the player controls a ship, which must dodge large numbers of obstacles and destroy large numbers of enemies. In the demonstration video, the fast moving projectiles are the obstacles which must be dodged - if the player hits any of these projectiles they die immediately. The player itself also fires projectiles, which damage enemies. The player is the little red sprite near the bottom of the screen. Props to ZUN for making games that people still play 20 years later.

If you want to see a prototype, you can check out the video demonstration below.
### [Video Demonstration (Legacy of Lunatic Kingdom)](https://youtu.be/9WElfhVE-Lk)

Theoretically, Twinject supports every Windows game in the Touhou Project. In order to support a game, four main components must be implemented, which I will briefly detail below.

## Components
### Graphical Control
Twinject requires control over the game graphics in order to draw debugging information which is useful in the development of the other components.

All mainline Touhou games can use Direct3D9 for their graphics (older games through the dxd8->dxd9 patch). Thus, this component is not game specific and only needs to be implemented once.

This component is implemented as a object wrapper delivered to the game via trampolining Direct3DCreate9.

### Input Control
Twnject requires control over the game input in order to control the player.

All mainline Touhou games can use DirectInput8 for their input (configurable with config.exe). Thus, this component is not game specific and only needs to be implemented once.

This component is implemented as a object wrapper delivered to the game via trampolining DirectInput8Create.

### Specific Data Processor
Twinject requires game data in order to be aware of the player's environment. This includes,

- Player location, velocity
- Bullet locations, velocities
- Powerup locations, velocities
- Enemy locations, velocities
- Laser functions

The game engines for each Touhou game vary wildly - this component is game specific and must be implemented for each game.

This component is usually implemented as a function hook.

### Algorithm
Twinject requires an algorithm to turn game data into player inputs. The following algorithms are currently supported:

- Method of Virtual Potential Fields
- Method of Velocity Obstacles
- Method of Vector-based Velocity Projection

I will explain these algorithms in detail in their own post.

## Implementation
Twinject provides a layer of abstraction between components, allowing new components to be easily created and connected together to create a functional automated player for a certain game.

Implementation of game-specific components is usually quite involved, and requires reverse-engineering the game. In this article, I will detail the required procedure to implement a game-specific component for Legacy of Lunatic Kingdom (LoLK).

# LoLK Specific Data Processor
I will detail the specific component required for processing data in LoLK.

## Collidable Locations
The player requires the locations and size of every collidable entity, so that it may dodge them. This includes the player, bullets, lasers, etc. Collidable entities may be demarcated into two types: function-based, and point-based.

The player and bullets are point-based, and are defined as a point with a radius (LoLK uses hit-circles).

Lasers are function-based, and are defined as a curve with a restricted domain.

For now, we will only talk about point-based collidable entities (PBCE).

### Obtaining Data
We want to trampoline a function which processes PBCEs once per frame. It would make sense for the game to have a function that checks for collisions between two PBCE.

[This website](http://cheater.seesaa.net/article/169529488.html) details many memory patches that can be used to modify the functionality of Touhou games. An interesting one is 
```
;無敵
00456540-C3     // set byte at 00456540 to C3
```
which renders the player invincible.

Taking a look in a disassembler (IDA), we can see that setting that byte causes a function to return immediately when called.

![ida death](https://i.imgur.com/HwGv6Oi.png)

*A disassembly in IDA, showing the function that the patch applies to, and its effect. (note that any variable names and comments were added in myself to help me reverse-engineer the game, and were not present beforehand)*

Setting a breakpoint in Ollydbg, we can see that this function is called after the player hits an obstacle. This function then carries out the death routine (reduce lives, set invincibility for a while, put player at origin etc.)

Checking cross references to this function, we find the following function:

![ida col](https://i.imgur.com/6fKBAT9.png)

*A rough decompilation of the function, with variable names annotated based on intuition*

We can see that this function checks for player collision with PBCEs using the square Euclidean distance check for circle collision. 
If the PBCEs collide, then it calls the death function we found earlier.

We can write a wrapper function that will extract the relevant data for Twinject to use:

![wrapper](https://i.imgur.com/7ubJU4E.png)

*Hook function for collision check function*

This wrapper function will be part of the component. Unfortunately the function is usercall, so we must write our own prolog and epilog code with inline assembly.