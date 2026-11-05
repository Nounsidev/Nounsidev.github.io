---
title: "3D Vampire Survivor - Procedural Map Generation "
date: 2026-10-24
permalink: /projects/star-light/
layout: single
---

## 1. Overview
<video autoplay loop muted playsinline style="max-width: 100%; border-radius: 6px;">
  <source src="/assets/images/Star-light-preview.mp4" type="video/mp4">
</video>
Rogue-lite game building with **Jobs System** and **Multithreading** to support up to 2000 enemies swarm with *Procedural Map Generation*  

Role: Solo Developer 
Tech Stack: Unity 6, C#, Job System, Multithreading  
Target Platform: PC  

#### 2. Key Technical Highlights
* **Procedural Map Generation:**
<video autoplay loop muted playsinline style="max-width: 100%; border-radius: 6px;">
  <source src="/assets/images/Map-Generation.mp4" type="video/mp4">
</video>
Generating procedural map using overlap noise map, height map and fall off map, then using multithread to help calculate meshes vertices and trigs, splitting into
different chunks before drawing the final terrain meshes on the main thread.  

* **Level of Details:**
<video autoplay loop muted playsinline style="max-width: 100%; border-radius: 6px;">
  <source src="/assets/images/LOD.mp4" type="video/mp4">
</video>
