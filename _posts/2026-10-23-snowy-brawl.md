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

<div style="text-align: left; margin: 10px 0;" markdown="1">
  Built on **Netcode for GameObjects (NGO)** as the core networking framework, integrated with **Unity Lobby** for seamless matchmaking and a **Relay server** utilizing UDP for low-overhead transport. To combat an average 120ms round-trip time (RTT) and mask network jitter, a hybrid client-side smoothing pipeline implements both interpolation and extrapolation to gracefully handle lag spikes.<br><br> 
</div>

<video autoplay loop muted playsinline style="max-width: 100%; border-radius: 10px;">
  <source src="/assets/images/Fight Clip.mp4" type="video/mp4">
</video>

<div style="text-align: left; margin: 10px 0;" markdown="1">
  All physics-driven movement utilizes delta synchronization to maintain consistent behavior across varying client frame rates while minimizing bandwidth overhead. Conversely, discrete, one-time gameplay actions are routed via client-to-server RPCs; the authoritative server then broadcasts the response to all connected peers. A deliberate network buffer delay is implemented on these actions to balance responsiveness and mitigate host advantage.<br><br>
   Furthermore, player state replication is optimized by synchronizing only the root transform and four key limb rotation variables; through empirical testing, utilizing **half-precision floats (16-bit)** and change-detection thresholds successfully minimized network payload overhead.
</div>

<br><br>
---
<br><br>

* **Cloud Saving:**
<div style="margin-left: 0px; width: 100%;">
  <img src="/assets/images/Cloud-Save.png" alt="Cloud Saving Dashboard" style="width: 100%; border-radius: 6px;">
</div>
<div style="text-align: left; margin: 10px 0;" markdown="1">
  Both Authentication and Cloud Saving are using UGS ( Unity Gaming Service) - which also provide lobby, player-matching, relay server. Although all the data is read-only for the owner client, it can be configured to be read-only by other clients to support system like Friends and Clan. 
</div>

<br><br>
---
<br><br>

* **Google Play Services:**
<div style="margin-left: 0px; width: 100%;">
  <img src="/assets/images/Google-Play-Snowy.png" alt="Description of the image" style="max-width: 100%; border-radius: 6px;">
</div>  
<div style="text-align: left; margin: 10px 0;" markdown="1">
 Successfully navigated full production lifecycles—spanning internal testing, closed testing, and a public Google Play Store release in December 2025. Integrated a suite of core Google Play APIs, including Play Integrity for security, Google Mobile Ads, Play Games Services, and Analytics to track real-time player telemetry and ensure production stability.
</div>

<br><br>
---
<br><br>

* **Update Manager:**
<div style="text-align: left; margin: 10px 0;" markdown="1">
  Engineered a centralized update manager to eliminate performance bottlenecks on memory-constrained mobile devices. By implementing a lightweight <strong>IUpdateObserver</strong> interface (along with <strong>IFixedUpdateObserver</strong> and <strong>ILateUpdateObserver</strong>), scripts register directly to custom tick loops—bypassing Unity's native C# interop overhead for standard Update() calls while enabling control over updating inactive object  or not.
</div>

```csharp
public class UpdateManager : MonoBehaviour
{
  private static List<IUpdateObserver> updateObservers = new();
  private static List<IUpdateObserver> pendingUpdateObservers = new();
  private static int currentUpdateObserverIndex;

   private void Update()
   {
       for (currentUpdateObserverIndex = updateObservers.Count - 1; currentUpdateObserverIndex >= 0; currentUpdateObserverIndex--)
       {
           updateObservers[currentUpdateObserverIndex].ObservedUpdate();
       }
  
       if (pendingUpdateObservers.Count == 0) return;
       updateObservers.AddRange(pendingUpdateObservers);
       pendingUpdateObservers.Clear();
   }

  public static void RegisterUpdateObserver(IUpdateObserver observer)
  {      
      pendingUpdateObservers.Add(observer);
  }
  
  public static bool UnregisterUpdateObserver(IUpdateObserver observer)
  {
      bool wasRemove =  updateObservers.Remove(observer);
      if(wasRemove) currentUpdateObserverIndex--;
      else wasRemove = pendingUpdateObservers.Remove(observer);
      return wasRemove;
  }
}
```

<br><br>
---
<br><br>

* **Flyweight Design Pattern:**
<div style="margin-left: 0px; width: 100%;">
  <img src="/assets/images/Scriptable Obj.png" alt="Description of the image" style="max-width: 100%; border-radius: 6px;">
</div>  

<div style="text-align: left; margin: 10px 0;" markdown="1">
  Decoupled all character and weapon statistics into dedicated ScriptableObjects, establishing a single source of truth for game data.
</div>

<video autoplay loop muted playsinline style="max-width: 100%; border-radius: 6px;">
  <source src="/assets/images/Book.mp4" type="video/mp4">
</video>

<div style="text-align: left; margin: 10px 0;" markdown="1">
  This modular architecture simplifies balance tweaks in the editor, streamlines data management, and ensures the codebase remains clean and scalable as the project expands.
</div>

<br><br>
---
<br><br>

* **Object Pooling:**
<div style="text-align: left; margin: 10px 0;" markdown="1">
  Architected a custom network object pooling system to eliminate runtime instantiation spikes and optimize client-server synchronization overhead. All network prefabs are pre-spawned under server authority upon initialization; when activated for gameplay, ownership is dynamically transferred to the target client to ensure responsive local control and clean authority management.
</div>

```csharp
public class NetworkObjectPooling : NetworkBehaviour
{
    HashSet<GameObject> m_Prefabs = new HashSet<GameObject>();
    Dictionary<GameObject, ObjectPool<NetworkObject>> m_PooledObjects = new Dictionary<GameObject, ObjectPool<NetworkObject>>();

    protected void RegisterPrefabInternal(GameObject prefab, int prewarmCount, int maxCount)
    {        
        NetworkObject CreateFunc()
        {
            return Instantiate(prefab).GetComponent<NetworkObject>();   
        }

        void ActionOnGet(NetworkObject networkObject)
        {
            networkObject.gameObject.SetActive(true);
        }

        void ActionOnRelease(NetworkObject networkObject)
        {
            networkObject.gameObject.SetActive(false);
        }

        void ActionOnDestroy(NetworkObject networkObject)
        {
            if (IsServer && networkObject!= null && !networkObject.Equals(null)) Destroy(networkObject.gameObject);
        }
        m_Prefabs.Add(prefab);

        m_PooledObjects[prefab] = new ObjectPool<NetworkObject>(CreateFunc, ActionOnGet, ActionOnRelease, ActionOnDestroy, defaultCapacity: prewarmCount, maxSize: maxCount);
        var prewarmNetworkObjects = new List<NetworkObject>();
        for (var i = 0; i < prewarmCount; i++)
        {
            prewarmNetworkObjects.Add(m_PooledObjects[prefab].Get());
        }
        foreach (var networkObject in prewarmNetworkObjects)
        {
            m_PooledObjects[prefab].Release(networkObject);
        }
       NetworkManager.Singleton.PrefabHandler.AddHandler(prefab, new PooledPrefabInstanceHandler(prefab, this));
    }
}
```
<div style="text-align: left; margin: 10px 0;" markdown="1">
  Extended Unity's native object pooling architecture to implement a Gradually Object Pool. Rather than pre-allocating a heavy footprint at startup, the pool initializes with a lean baseline and dynamically instantiates additional instances only when required. This balances memory efficiency by preventing early-game bloat while handling systems that scale progressively throughout a session.
</div>

```csharp
 public GradualObjectPooling(Func<T> createFunc, Action<T> onActionGet, Action<T> onActionRelease, int capacity) : base(createFunc, onActionGet, onActionRelease, capacity)
 {
     OnActionGet = onActionGet;
     OnActionRelease = onActionRelease;
     CreateFunc = createFunc;
     poolCapacity = capacity;
     poolingList = new List<T>(capacity);
 }

 public override T Get()
 {
     T poolObject;
     //Reusing the same object if it available in the pool
     if (freshlyReleashObject != null)
     {
         poolObject = freshlyReleashObject;
         freshlyReleashObject = null;
     }
     else if (poolingList.Count == 0 && objectCount <= poolCapacity)
     {
         poolObject = CreateFunc();
         objectCount++;
     }
     else
     {
         int index = poolingList.Count - 1;
         poolObject = poolingList[index];
         poolingList.RemoveAt(index);
     }
     OnActionGet?.Invoke(poolObject);
     return poolObject;
 }

 public override void Release(T poolObject)
 {           
     OnActionRelease?.Invoke(poolObject);
     if (freshlyReleashObject == null)
     {
         freshlyReleashObject = poolObject;
         return;
     }

     poolingList.Add(poolObject);
 }
```

<br><br>
---
<br><br>

* **Customizable Controls:**
<video autoplay loop muted playsinline style="max-width: 100%; border-radius: 6px;">
  <source src="/assets/images/Customize.mp4" type="video/mp4">
</video>
<div style="text-align: left; margin: 10px 0;" markdown="1">
  Engineered a custom UI preference system empowering players to tailor their layout and settings to individual playstyles. Settings are serialized and stored locally via JSON configuration files, ensuring that multi-device cross-play environments seamlessly adapt to varying hardware resolutions.
</div>

