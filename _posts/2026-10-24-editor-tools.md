---
title: "Unity Editor Tools"
date: 2026-10-24
permalink: /projects/editor-tools/
layout: single
---

## 1. Overview
Over the year of building game on Unity, I have made quite a few engine tools with the sole purpose to speed up my development progress, skipping over long dreadful and repetitive tasks. 
All tools list below are builts with Unity Editor library.


## 2. Key Technical Highlights
* **Meshes Baking Tool:**
<video autoplay loop muted playsinline style="max-width: 100%; border-radius: 6px;">
  <source src="/assets/images/Mesh-Tool.mp4" type="video/mp4">
</video>
This was built for my 3D vampire Survivors like project as meshes deforming is computationally expensive for 1000+ meshes. Animation can be create and import from software like Blender, using 
default Unity's animator, with this you can select the frame rate, bakes meshes and store them in a separated scriptable object for each animations. From a simple script you can loop through this animation for 
thousands of models without having to worry about performance.

* **Automatic Enum Builder:**

* **Ragdoll Component Synchronizer:**
