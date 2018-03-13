---
layout: post
title: Using Constrained Velocity Obstacles for Collision Avoidance
keywords: touhou, twinject, motion planning
description: A quick technical description regarding the use of constrained velocity obstacles in motion planning and collision prediction
---

# Contents
{:.no_toc}
* Table of Contents
{:toc}

[Twinject](https://github.com/netdex/twinject) is an automated player for the bullet hell games from the *Touhou Project*. Instead of using computer vision techniques to extract data from the game, a DLL is injected and the relevant game data is directly extracted through trampolined functions.

In this post, I will go over a specific algorithm implemented in Twinject for collision avoidance, which is a constrained version of [velocity obstacle](https://en.wikipedia.org/wiki/Velocity_obstacle) approach. This algorithm uses a greedy optimization approach in conjunction with the VO method to create a real-time player algorithm.

# Brief Overview
> In robotics and motion planning, a velocity obstacle, commonly abbreviated VO, is the set of all velocities of a robot that will result in a collision with another robot at some moment in time, assuming that the other robot maintains its current velocity.[1] If the robot chooses a velocity inside the velocity obstacle then the two robots will eventually collide, if it chooses a velocity outside the velocity obstacle, such a collision is guaranteed not to occur.[1]
>
> &mdash; Velocity Obstacle, Wikipedia

We extend the idea of the velocity obstacle (VO) to encompass all the possible actions a player may execute. Some actions which Twinject considers are,

- Player motion (normal)
    - Cardinals (NSEW), diagonals
- Player motion (focused)
    - Cardinals (NSEW), diagonals
- Bomb 

This is why the problem is constrained. Since there are a very finite number of actions the player may execute, we may consider all of them.

# Implementation
Assume we have knowledge of areas that we want to avoid, and areas we want to target. We define their collision domain (CD) to be a closed boundary in a two dimensional space, encompassing the area we want to avoid. We want this CD to fit the actual collision area as closely as possible.

For ease of implementation, we will consider two possible shapes for CDs: the circle, and the axis-aligned bounding box (AABB). CDs also possess a velocity - we will assume that all velocities are linear in nature.

Thus, a CD can be defined by the following attributes:
- Position
- Dimensions (shape, size)
- Velocity
- Avoid/target

## Collision Prediction
We restrict the problem to only consider CDs of a circular or AABB shape. This is because the hitboxes in the Touhou Project are shaped as either circles or AABBs, depending on the specific game. Our algorithm must support both shapes in order to support all games.

### AABB
An AABB in this model may be described by an origin point, size, and velocity.

Let $$\vec{p_1}, \vec{p_2}$$ be the position of the top-left corners of two AABBs.  
Let $$\vec{s_1}, \vec{s_2}$$ be their respective sizes.
Let $$\vec{v_1}, \vec{v_1}$$ be their velocities in pixels/frame.

Let $$a,b$$ be two AABBs. We define a function $$d(a, b)$$, known as the discriminant of the AABB collision.  
$$
\begin{align*}
d(a, b)=
\begin{cases}
0      & \text{ if $a$ is already colliding with $b$}\\
\gt0   & \text{ if $a$ will collide with $b$ in the future}\\
\lt0   & \text{ if $a$ will never collide with $b$}
\end{cases}
\end{align*}
$$

`TODO: finish this blog post`