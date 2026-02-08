[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/S8yfa_LZ)
[![Open in Visual Studio Code](https://classroom.github.com/assets/open-in-vscode-2e0aaae1b6195c2367325f4f02e2d04e9abb55f0b24a779b69b11b9e10269abc.svg)](https://classroom.github.com/online_ide?assignment_repo_id=22573813&assignment_repo_type=AssignmentRepo)
# Lab 01 — Simulating Human-Centered XR Interactions

---

## Lab Overview

Extended Reality (XR) is not only about wearing headsets — it is about how technology interacts with human senses such as vision, hearing, and touch.

In this lab, you will simulate core XR interaction techniques using Unity without requiring any VR devices.

By the end of this session, you will understand how gaze, feedback, and multisensory cues create the powerful feeling of presence in immersive environments.

---

## Learning Outcomes

After completing this lab, students should be able to:

- Understand gaze-based interaction  
- Simulate eye-tracking using the camera  
- Apply sensory feedback to improve realism  
- Explain how multisensory input increases immersion  
- Design simple human-centered XR interactions  

---

## Duration

2 Hours

---

## Requirements

Install **before** the lab:

- Unity Hub  
- Unity 2022.3 LTS (recommended for stability)  
- Create a 3D Core Project  

**Do NOT import external packages.**  
This lab uses only built-in Unity tools to avoid technical issues.

---

## Important Rule

Do NOT rush.

Follow steps in order.

If something does not work, recheck the step before calling the lecturer. Most Unity errors happen from skipped steps.

---

# Part 1 — Scene Setup 

---

## Step 1 — Create a New Project

Open Unity Hub → New Project

Select: 3D Core


Name the project: XR_Interaction_Lab


Click **Create Project**.

---

## Step 2 — Prepare the Scene

Inside **Hierarchy**:

Delete: Directional Light (optional)


Keep:

- Main Camera  
- Plane (we will create it)

---

## Step 3 — Create the Ground

Hierarchy → Right Click → 3D Object → Plane

Reset Transform: `Position: 0,0,0` | `Rotation: 0,0,0` | `Scale: 1,1,1`


---

## Step 4 — Create the Interaction Cube

Hierarchy → Right Click → 3D Object → Cube

Set Position: `X: 0` | `Y: 1` | `Z: 5`


The cube should now be directly in front of the camera.

---

## Step 5 — Improve Visibility

Select **Cube → Inspector → Mesh Renderer → Material**

Create a new material:

Project → Right Click → Create → Material

Name it: `M_Cube`


Choose a bright color (blue recommended).

Drag the material onto the cube.


# Part 2 — Gaze Interaction (Main Task)

---

## Concept

Eye-tracking detects where a user is looking.

Since we do not have eye-tracking hardware, we will use the camera as the eyes.

If the camera center points at the object, the system assumes the user is looking at it.

This is called: `Intent Detection`

---

## Step 1 — Create the Script

Project → Right Click → Create → C# Script

Name the script: `GazeInteraction`


Double click to open it.

Delete everything inside the script and paste the following code:

```csharp
using UnityEngine;

public class GazeInteraction : MonoBehaviour
{
    private Renderer objectRenderer;
    private Color originalColor;

    public Color gazeColor = Color.green;

    void Start()
    {
        objectRenderer = GetComponent<Renderer>();
        originalColor = objectRenderer.material.color;
    }

    void Update()
    {
        Ray ray = Camera.main.ViewportPointToRay(new Vector3(0.5f, 0.5f, 0));
        RaycastHit hit;

        if (Physics.Raycast(ray, out hit))
        {
            if (hit.transform == transform)
            {
                objectRenderer.material.color = gazeColor;
            }
            else
            {
                objectRenderer.material.color = originalColor;
            }
        }
        else
        {
            objectRenderer.material.color = originalColor;
        }
    }
}
```
Save the script and return to Unity.

---

## Step 2 — Attach the Script

Drag the **GazeInteraction** script onto the cube in the Hierarchy.

---

## Step 3 — Test the Interaction

Click the **Play** button.

Now observe the behavior:

- Look at the cube → it turns green  
- Look away → it returns to the original color  

You have now simulated eye tracking using the camera.

## Mini Challenge (Do Not Skip)

Modify the script so the cube becomes **red** instead of green.

Hint: Change `Color.green` to `Color.red`.

---

# Part 3 — Gaze Selection System

Looking is not enough. XR systems often use:

Dwell Time Selection (looking for a short time = select)

---

## Step 1 — Update Script

Replace your script with the following code:

```csharp
using UnityEngine;

public class GazeInteraction : MonoBehaviour
{
    private Renderer objectRenderer;
    private Color originalColor;

    public Color gazeColor = Color.yellow;
    public float dwellTime = 2f;

    private float timer;

    void Start()
    {
        objectRenderer = GetComponent<Renderer>();
        originalColor = objectRenderer.material.color;
    }

    void Update()
    {
        Ray ray = Camera.main.ViewportPointToRay(new Vector3(0.5f, 0.5f, 0));
        RaycastHit hit;

        if (Physics.Raycast(ray, out hit) && hit.transform == transform)
        {
            objectRenderer.material.color = gazeColor;
            timer += Time.deltaTime;

            if (timer >= dwellTime)
            {
                transform.localScale *= 1.3f;
                timer = 0;
            }
        }
        else
        {
            objectRenderer.material.color = originalColor;
            timer = 0;
        }
    }
}
```
## Step 2 — Test Again

Look at the cube for **2 seconds**.

The cube should grow.

You have now created button-free interaction, which is commonly used in accessibility-focused XR.

---

# Part 4 — Add Sensory Feedback

XR feels more realistic when multiple senses are engaged. In this section, you will add a sound cue to simulate sensory feedback.

---

## Step 1 — Add Audio Feedback

Hierarchy → Right Click → Audio → Audio Source

Rename the Audio Source: `SelectSound`


In the Inspector:

- Disable **Play On Awake**

Import any short click sound (Unity provides free options), then drag it into the **AudioClip** field of `SelectSound`.

---

## Step 2 — Modify Script

Open the `GazeInteraction` script and add this variable near the top of the class:

```csharp
public AudioSource selectAudio;
```

Inside the dwell trigger (inside if (timer >= dwellTime)), add: `selectAudio.Play();`

Your dwell trigger block should look like this:

```csharp
if (timer >= dwellTime)
{
    selectAudio.Play();
    transform.localScale *= 1.3f;
    timer = 0;
}

```

Save the script.

## Step 3 — Link the Audio Source

Select the cube in the Hierarchy.

In the Inspector, locate the **GazeInteraction** component.

Drag the `SelectSound` object from the Hierarchy into the **Select Audio** field.

---

## Step 4 — Test

Click **Play**.

Observe the behavior:

- Look at the cube  
- Wait 2 seconds  
- You should hear the sound  
- The cube grows  

This demonstrates multisensory design, where visual and audio feedback work together to enhance the interaction.

---

# Part 5 — Presence Experiment

Create a second cube that does **not** include sound.

Compare the experience between the two cubes:

- Cube A: color + scale + sound  
- Cube B: color + scale only  

Consider the following question:

Which one felt more realistic?

This concept is known as **sensory alignment** — engaging more senses typically strengthens the feeling of presence.

---

## Final Challenge (Highly Recommended)

Create three cubes and test them:

- Cube 1: color change only  
- Cube 2: color change + scale change  
- Cube 3: color change + scale change + sound  

Observe which version feels most immersive and explain your reasoning.

---

## Reflection Questions (Submit)

1. What is gaze-based interaction?  
2. Why is dwell time useful?  
3. How does sensory feedback improve realism?  
4. Which cube felt most immersive and why?  

---

## Expected Output

By the end of the lab, you should have:

- A gaze-reactive object  
- Selection without clicking  
- Sensory feedback  
- A basic understanding of presence  

---

## Common Mistakes

If the cube does not change color, check the following:

- The script is not attached to the cube  
- You are viewing from the Scene view instead of the Game view  
- The cube does not have a Collider (it should be added by default)  
- The camera is not tagged as `MainCamera`  

---



## GitHub Submission Instructions (only students who needed) 

### 1) Place the `.gitignore` in the correct location
1. Download the provided `.gitignore` file.
2. Copy it into the **root** of your Unity project folder (same level as `Assets/`, `Packages/`, and `ProjectSettings/`).

Your folder should look like: `XR_Interaction_Lab/
.gitignore
Assets/
Packages/
ProjectSettings/`


---

### 2) Create a GitHub repository
1. Go to GitHub and create a new repository.
2. Name it (example): `XR_Interaction_Lab`.
3. Keep it Public or Private (either is fine).
4. Copy the repository **HTTPS URL**.

---

### 3) Open Terminal / Git Bash inside the project folder
- Windows: Right click inside the project folder → **Open Git Bash here**
- Mac/Linux: Open Terminal and `cd` into the project folder

---

### 4) First-time push (most common case)
Run these commands **one by one**:

```bash
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin <PASTE_YOUR_GITHUB_REPO_URL_HERE>
git push -u origin main
```
After pushing, refresh the GitHub repository page to confirm the files are uploaded.

### 5) Pushing Updates Later (After You Make Changes)

Use the following commands whenever you update your project:

```bash
git add .
git commit -m "Updated lab work"
git push
```

### 6) If You Already Pushed Large Unity Folders by Mistake

If folders such as `Library`, `Temp`, or `Obj` were uploaded, remove them from GitHub while keeping them on your computer:

```bash
git rm -r --cached Library Temp Obj
git add .
git commit -m "Remove ignored Unity folders"
git push
```
### 7) Common Errors and Fixes

#### Error: `remote origin already exists`

```bash
git remote remove origin
git remote add origin <PASTE_YOUR_GITHUB_REPO_URL_HERE>
git push -u origin main
```
#### Error: `failed to push some refs`

```bash
git pull --rebase origin main
git push
```

---
SE4051 - Trends in Digital Media - Lab 01









