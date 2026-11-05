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

## 2. Key Technical Highlights
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
Terrain meshes are divided into different chunk, they are then being optimized bases on player position and sight of line. Only the main mesh that the player are on had a physic collider.
Physic collider also uses the same mesh as the highest LOD mesh  
  
* **Jobs System:**
```csharp
private void EnemyHandler()
{
    climbCounter += Time.fixedDeltaTime;

    int activeEnemyCount = enemies.Count;
    if (activeEnemyCount <= 0) return;

    bool isClimbReach = climbCounter >= climbCheckRate;
    NativeArray<float3> enemyPositions = new NativeArray<float3>(activeEnemyCount, Allocator.TempJob);
    NativeArray<float> outSqrDistances = new NativeArray<float>(activeEnemyCount, Allocator.TempJob);
    NativeQueue<int> damageQueue = new NativeQueue<int>(Allocator.TempJob);

    Vector3 playerPos = playerTransform.position;

    for (int i = 0; i < activeEnemyCount; i++)
    {
        enemyPositions[i] = enemies[i].rb.position;
        if (isStoppingEnemy)
        {
            continue;
        }
        MoveEnemy(i, playerPos, (Vector3)enemyPositions[i], enemies[i].enemySO.speed, isClimbReach);
    }

    if (isClimbReach) climbCounter = 0f;

    EnemyNearestAndDamageJob job = new EnemyNearestAndDamageJob
    {
        EnemyPositions = enemyPositions,
        PlayerPosition = playerPos,
        DeltaTime = Time.fixedUnscaledDeltaTime,
        DamageDelay = damageDelay,
        SqrDamageDistance = damageDistance * damageDistance,
        DamageCounters = damageCounters,
        OutSqrDistances = outSqrDistances,
        DamageQueue = damageQueue.AsParallelWriter()
    };

    JobHandle jobHandle = job.Schedule(activeEnemyCount, 64);
    jobHandle.Complete();

    EnemyDistanceHandler(activeEnemyCount, outSqrDistances);

    enemyAnimation.ResetAnimTime();

    while (damageQueue.TryDequeue(out int id))
    {
        if (++iterations > maxSafetyIterations)
        {
            Debug.LogError("Infinite loop detected in damageQueue processing!");
            break;
        }
        Vector3 distanceV3 = playerPos - enemies[id].enemyTransform.position;
        Vector3 knockBackForce = distanceV3.normalized * enemies[id].enemySO.knockBackForce;
        playerHealth.TakeDamageAndKnockBack(enemies[id].enemySO.damage, knockBackForce);
    }
    iterations = 0;

    enemyPositions.Dispose();
    outSqrDistances.Dispose();
    damageQueue.Dispose();
}
```
Combining Multithreading and Job System to manage up to 2000+ enemies, 200+ projectiles and 500+ collectibles without dropping frame rate. Seperating threading logic from movement pattern for easier management and scalability.

