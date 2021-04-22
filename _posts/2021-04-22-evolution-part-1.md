---
title: "Random map generation in Godot 🗺"
excerpt: "I show a basic setup to generate a random 2D map in Godot."
layout: default
math: false
categories: Simulations
date: 2021-04-22
---

Recently I was interested in how to create random maps in Godot. The topic of creating something, like a map, by just a few lines of code has always interested me. I haven't done anything like that yet, but I'm working on a small project where I can easily implement that.

The basic idea is simple, you create a noise map by a random noise algorithm (like the "classic" Perlin noise or the improved and open source version, the [OpenSimplex Noise](https://en.wikipedia.org/wiki/OpenSimplex_noise)) and based on this noise the map is created. Noise algorithms are a very interesting topic in their own right, which I may delve into in the future, but for now let's move on. In Godot, you can easily create noise using the [`OpenSimplexNoise`](https://docs.godotengine.org/de/stable/classes/class_opensimplexnoise.html) class. You can use the `get_image()` method to create a noise image, which looks for example like this:

{:refdef: style="text-align: center;"}
[![](/assets/images/blog/2021-04-22/noisemap.png){:height="100%" width="100%"}](/assets/images/blog/2021-04-18/noisemap.png)
{: refdef}

You probably ask yourself how do we get from this to a map? Well, as mentioned the basic idea is simple, for each pixel we have now a `float` value between 0 and 1. One can now assign for each partial range from 0 to 1 (e.g. 0.25 to 0.50) a specific tile (e.g. grass).

_Side Note:_ For my project I used a tileset from [kenney.nl](https://www.kenney.nl), which is very good for prototyping. 

This means, you take the noise image, loop through each pixel and based on the value of this pixel you assign a specific tile. This is the basic idea, you can check a playable example (works best on desktop) on my [itch.io page](https://divinism.itch.io), or click the link down below. I also have the source code on my [GitHub page](https://github.com/divin) check [Evolution](https://github.com/divin/Evolution) as this example is a part of a small project I'm working on.

<p align="center">
<iframe frameborder="0" src="https://itch.io/embed/1005119?bg_color=222222&amp;fg_color=eeeeee&amp;border_color=363636" width="552" height="167"><a href="https://divinism.itch.io/map-generation-example">Map Generation Example by divinism</a></iframe>
</p>