---
title: "Snowy Brawl — 2D Physics Multiplayer"
date: 2026-10-24
permalink: /projects/snowy-brawl/
layout: single
---

## 1.  Overview
<video autoplay loop muted playsinline style="max-width: 100%; border-radius: 10px;">
  <source src="/assets/images/snowy_brawl_preview.mp4" type="video/mp4">
</video>
Fast-paced 2D multiplayer ragdoll physics game using **Netcode for Gameobjects** and **Objects Pooling** to maintain 60 FPS across multi-client sessions. Allowing up 
to 4 players per section with customizable teams, weapons and gamemodes
  
Role: Solo Developer  
Tech Stack: Unity 6, C#, Netcode for GameObjects, Unity Gaming Services, Unity Ads, Google Play Services  
Target Platform: Mobile  
Links: [Google Play Store](https://play.google.com/store/apps/details?id=com.NounsiDev.Snowy&pcampaignid=web_share)

## 2. Key Technical Highlights
* **Networking:**
<video autoplay loop muted playsinline style="max-width: 100%; border-radius: 10px;">
  <source src="/assets/images/Lobby.mp4" type="video/mp4">
</video>

<video autoplay loop muted playsinline style="max-width: 100%; border-radius: 10px;">
  <source src="/assets/images/Fight Clip.mp4" type="video/mp4">
</video>

<div style="text-align: left; margin: 10px 0;">
  This game has been undergone internal testing, closed testing and finally launching on Google Play store since Dec 2025. Which also means multiple Google Play API including Integrity, Ads, Play Services, Analytics are implemented in the game.
</div>

---

* **Cloud Saving:**
<div style="margin-left: 0px; width: 100%;">
  <img src="/assets/images/Cloud-Save.png" alt="Cloud Saving Dashboard" style="width: 100%; border-radius: 6px;">
</div>
<div style="text-align: left; margin: 10px 0;">
  Both Authentication and Cloud Saving are using UGS ( Unity Gaming Service) - which also provide lobby, player-matching, relay server. Although all the data is read-only for the owner client, it can be configured to be read-only by other clients to support system like Friends and Clan. 
</div>

---

* **Google Play Services:**
<img src="/assets/images/Google-Play-Snowy.png" alt="Description of the image" style="max-width: 100%; border-radius: 10px;">
<div style="text-align: left; margin: 10px 0;">
  This game has been undergone internal testing, closed testing and finally launching on Google Play store since Dec 2025. Which also means multiple Google Play API including Integrity, Ads, Play Services, Analytics are implemented in the game.
</div>

---

* **Object Pooling:**

---

* **Flyweight Design Pattern:**
<img src="/assets/images/Scriptable Obj.png" alt="Description of the image" style="max-width: 100%; border-radius: 10px;">
<video autoplay loop muted playsinline style="max-width: 100%; border-radius: 6px;">
  <source src="/assets/images/Book.mp4" type="video/mp4">
</video>
<div style="text-align: left; margin: 10px 0;">
  This game has been undergone internal testing, closed testing and finally launching on Google Play store since Dec 2025. Which also means multiple Google Play API including Integrity, Ads, Play Services, Analytics are implemented in the game.
</div>
---

* **Customizable Controls:**
<video autoplay loop muted playsinline style="max-width: 100%; border-radius: 6px;">
  <source src="/assets/images/Customize.mp4" type="video/mp4">
</video>
