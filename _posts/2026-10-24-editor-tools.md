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
Managing 200+ audio clips using string lookup or dragging individual clip each time you want to use is time-consuming and prone to typing error. Instead this tool will help converts your audio clip names into a enum that you can call everywhere in your codebase.
```csharp
private static string GenerateEnumList(List<SerializedObject> serializedObjects, string listPropertyName, string enumType)
{
    var enumNames = new List<string>();

    foreach (var serializedSO in serializedObjects)
    {
        SerializedProperty listProperty = serializedSO.FindProperty(listPropertyName);

        if (listProperty == null || !listProperty.isArray)
        {
            Debug.LogError("Could not find the 'audioSettings' list property on the SO.");
            return string.Empty;
        }


        // Iterate through the SerializedProperty list
        for (int i = 0; i < listProperty.arraySize; i++)
        {
            SerializedProperty clipProperty = listProperty.GetArrayElementAtIndex(i);
            if (clipProperty.objectReferenceValue == null)
            {
                Debug.LogWarning($"Skipping list entry at index {i} in '{listPropertyName}' because the AudioClip slot is empty.");
                continue;
            }

            // Get the file name from the AudioClip asset reference
            Object audioClipObject = clipProperty.objectReferenceValue;

            // Get the full path to the asset
            string assetPath = AssetDatabase.GetAssetPath(audioClipObject);

            // Extract the file name without the extension
            string fileName = Path.GetFileNameWithoutExtension(assetPath);
            string safeName = fileName.Replace(" ", "").Replace("-", "");

            if (!enumNames.Contains(safeName))
            {
                enumNames.Add(safeName);
            }
        }
    }

    if (enumNames.Count == 0) return string.Empty;

    // Build the C# code content
    StringBuilder sb = new();
    sb.AppendLine($"public enum {enumType}");
    sb.AppendLine("{");

    for (int i = 0; i < enumNames.Count; i++)
    {
        // Use the index for hard-coded value assurance
        sb.AppendLine($"\t{enumNames[i]} = {i},");
    }

    sb.AppendLine("}");

    //Return the generated enum string
    return sb.ToString();
}
```

