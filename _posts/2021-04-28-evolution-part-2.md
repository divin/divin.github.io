---
title: "Simulating Herbivores and Carnivores 🦁🐮"
excerpt: "I expanded my simulation of the previous post with some animals."
layout: default
math: false
categories: Simulations
date: 2021-04-28
---

I added some more features to my previous map generation tool, now I can adjust the ingame time by changing the `Engine.time_scale` factor of Godot. I also added some entities to this simulation. We now have herbivores that only eat plants, and carnivores that hunt the herbivores to get meat. I added several states that determine what the entity is doing, `WALK`, `HUNT`/ `FLEE`, `EAT`, `MATE` and `DEAD`. In the `WALK` state, the entity walks for 1 second in a random direction, managed by a `Timer` object in Godot. The `HUNT`/ `FLEE` states are for the carnivore / herbivore respectively. When a herbivore is near the carnivore, the carnivore starts chasing the herbivore while the herbivore tries to escape. Since the carnivore's speed is slightly faster, the carnivore can chase down the herbivore in most cases. I may have to add some sort of endurance in the future. The entities can also reproduce, for this the state `MATE` creates a new tiny baby entity. The other states, `EAT` and `DEAD`, only feed the entity or put it dead.

Each entity has a kind of _health_ determined by `hunger` and `thirst`, these values constantly decrease with time, but can be replenished by eating or drinking. So the entities have to eat and drink to stay alive, or in the case of the herbivore, to avoid being hunted down. Below you can see a screenshot of the current state:

{:refdef: style="text-align: center;"}
[![](/assets/images/blog/2021-04-28/screenshot.png){:height="100%" width="100%"}](/assets/images/blog/2021-04-18/noisemap.png)
{: refdef}

_Sidenote:_ For my project I used a tileset from [kenney.nl](https://www.kenney.nl), which I recommend for prototyping. 

You can see a playable example (works best on the desktop) on my [itch.io page](https://divinism.itch.io), or click the link below. I also have the source code on my [GitHub page](https://github.com/divin), see [Evolution](https://github.com/divin/Evolution), as this example is part of a small project I'm working on.

{:refdef: style="text-align: center;"}
[Play Map Generation Example on Itch.io](https://divinism.itch.io/herbivores-carnivores-example)
{: refdef}