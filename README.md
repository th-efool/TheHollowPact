# TheHollowPact

**Engine:** `Unreal Engine 5.5.4`  
**Mode:** `Multiplayer (Listen Server)`  
**Genre:** `Third-Person Shooter RPG`  
**Perspective:** `Over-the-shoulder third person`  
**Target Platform:** `PC (with scalability for Console)`

---

## Table of Contents
1. [Multiplayer Architecture](#1-multiplayer-architecture)
2. [Character System](#2-character-system)
3. [Weapons & Combat](#3-weapons--combat)
4. [AI Systems](#4-ai-systems)
5. [User Interface (HUD)](#5-user-interface-hud)
6. [Audio System](#6-audio-system)
7. [Prop Hunt Game Mode](#7-prop-hunt-game-mode)

---

## 1. Multiplayer Architecture

| Class | Responsibility | Notes |
|-------|---------------|-------|
| **AGameModeBase / AGameMode** | Defines game rules and win/loss conditions (server-only) | Only exists on the server |
| **AGameStateBase / AGameState** | Tracks match state, time, score (server + replicated to all clients) | Syncs global data to clients |
| **APlayerController** | Represents the player's input and client control logic | First class a player "owns" |
| **APlayerState** | Holds player-related data (name, score, team, etc.) | Exists on server and all clients |
| **APawn / ACharacter** | The controllable actor (body) in the world | Possessed by PlayerController |
| **AHUD** | Basic on-screen UI manager (legacy) | Generally replaced by UMG in modern projects |
| **UUserWidget** | UI elements created via Unreal Motion Graphics (UMG) | For health bars, ammo, HUDs, menus |

### Architecture Diagrams

<div align="center">

![Network Architecture Diagram 1](https://github.com/th-efool/TheHollowPact/blob/main/docs/screenshot20250527195357.png?raw=true)

![Network Architecture Diagram 2](IMAGE_URL_HERE)

![Network Architecture Diagram 3](https://github.com/th-efool/TheHollowPact/blob/main/docs/batch/Pasted%20image%2020250527144749.png?raw=true)

</div>

---

## 2. 🦾 Character System

### Complex Locomotion System

<div align="center">

![Character Locomotion System](https://github.com/th-efool/TheHollowPact/blob/main/docs/batch/Pasted%20image%2020250527144925.png?raw=true)

</div>

Our character system features multiple states with seamless transitions:

#### 🏃 Movement States
- **UnArmed State**
  - UnArmed Normal
  - UnArmed Crouch
  - UnArmed Jump
  - UnArmed Run

- **🏹 Bow State**
  - Bow Normal
  - Bow Crouch
  - Bow Jump
  - Bow Running

- **🔫 Rifle State**
  - Rifle Normal
  - Rifle Crouch
  - Rifle Jump
  - Rifle Running

- **⚔️ Sword State**
- **😴 Idle State**
- **💀 Death State**

> The PawnBlueprint holds the variables that are used to set values in the AnimationBlueprint attached to the SkeletalMesh Component.

### 👁️ Head Gaze Tracking

<div align="center">

![Head Gaze Tracking Demo](https://github.com/th-efool/TheHollowPact/blob/main/docs/UnrealEditor_oDT4WUAzmW1-ezgif.com-optimize.gif?raw=true)

</div>

We dynamically adjust the **rotation value** of neck and upper spines of our character, according to where the player is looking to give a more dynamic feel.

<div align="center">

![Head Gaze System Diagram](https://github.com/th-efool/TheHollowPact/blob/main/docs/batch/Pasted%20image%2020250527144956.png?raw=true)

</div>

### 🚶 Dynamic Movements (Footsteps, Turning)

We have implemented dynamic blending between movements in different directions to give a more realistic feel.

---

## 3. ⚔️ Weapons & Combat

### Core Combat Features
- **🎥 Combat Camera** - Dynamic camera system for combat scenarios
- **🔫 Weapon Systems**
  - Equip/Unequip mechanics
  - Weapon swapping system
- **🤝 Interaction System**
  - Weapon/Ammo/Grenade pickup and drop
- **💥 Shooting System**
  - AmmoInventory management
  - Reload mechanics
  - Server-side firing validation
  - Combat animations
  - Visual effects (VFX)
  - Muzzle flash system

---

## 4. 🤖 AI Systems

<div align="center">

![AI Systems Overview](https://github.com/th-efool/TheHollowPact/blob/main/docs/batch/Pasted%20image%2020250527165943.png?raw=true)

</div>

### 🐉 Dragon AI Logic

```mermaid
flowchart TD
    A[ROAM] --> B{Player?}
    B -->|No| A
    B -->|Far| C[FLY]
    B -->|Close| D[Ground Combat]
    
    C --> E{Distance?}
    E -->|Far| F[GLIDE]
    E -->|Close| G{Player Air?}
    
    F --> H{Distance?}
    H -->|Close| I[FLY]
    H -->|Far| F
    
    I --> G
    G -->|Ground| D
    G -->|Air| J[Air Combat]
    
    D --> K{Result?}
    K -->|Escaped| C
    K -->|Dead| A
    K -->|Fight| D
    
    J --> L{Result?}
    L -->|Escaped| C
    L -->|Dead| A
    L -->|Landed| D
    L -->|Fight| J
```

### 🎭 Animation System

<div align="center">

![AI Animation System](https://github.com/th-efool/TheHollowPact/blob/main/docs/batch/Pasted%20image%2020250527165609.png?raw=true)

</div>

### 🐺 Creature Collection
- **Barghest** - Mythical hound creature
- **Centaur** - Half-human, half-horse warrior
- **Kraken** - Sea monster with tentacle attacks
- **Griffon** - Eagle-lion hybrid with aerial combat

<div align="center">

![Creature Collection](https://github.com/th-efool/TheHollowPact/blob/main/docs/batch/Pasted%20image%2020250527170018.png?raw=true)

</div>

### 🐙 Kraken System

<div align="center">

![Kraken AI System](https://github.com/th-efool/TheHollowPact/blob/main/docs/batch/Pasted%20image%2020250527165725.png?raw=true)

</div>

### ⚔️ SwordsMaster AI

<div align="center">

![SwordsMaster Combat](https://github.com/th-efool/TheHollowPact/blob/main/docs/UnrealEditor_rJ8zmcuKa9.gif?raw=true)

</div>

```mermaid
graph LR
    R[ROAM] --> A[ALERT]
    A --> C[COMBAT]
    C --> A1[ATK1]
    C --> A2[ATK2]
    C --> A3[ATK3]
    C --> A4[ATK4]
    A1 --> C
    A2 --> C
    A3 --> C
    A4 --> C
    A --> R
```

---

## 5. 🖼️ User Interface (HUD)

<div align="center">

![Main HUD Interface](https://github.com/th-efool/TheHollowPact/blob/main/docs/screenshot20250527172748.png?raw=true)

![Secondary HUD Interface](https://github.com/th-efool/TheHollowPact/blob/main/docs/2025%2001%2025%2023%2021%2015%203-3%20screenshot.png?raw=true)

</div>

### 🧭 Compass System
For the compass HUD, we use a texture that loops and rolls over to create the compass effect. The position & offset on the texture is changed through the controller class.

<div align="center">

![Compass System](IMAGE_URL_HERE)

</div>

### 🗺️ MiniMap System
We implemented two modes:

1. **Camera Mode (Active)** - Camera held above the player's head, output passed through color grading filter (rendered at low resolution for performance)
2. **Texture Mode** - Track character location and map it onto premade level texture

### 📢 MessageBox System
The `showMessageBox()` function can be called through the `PlayerController`.

### 📊 Status Displays
- **Weapon/Health/Ammo/Grenade** - Values stored within the pawn, HUD updates on value changes
- **ClipAmmo & InventoryAmmo** - Changes based on currently equipped weapon

### 🎯 HitMarker System
When players land successful hits, red hitmarkers appear to provide visual feedback.

<div align="center">

![MultiplayerMenu](https://github.com/th-efool/TheHollowPact/blob/main/docs/UnrealEditor_Uvwf7hT61F.gif?raw=true)

</div>

---

## 6. 🔊 Audio System

### 🌍 Ambient Audio Objects
Special actors that trigger when players enter a certain radius, responsible for playing background ambient noise.
> Location: `Content/SoundAmbience/Apocalypse`

### 🎧 Spatial Audio Attenuation System

#### **Core Features:**
- Distance-based volume attenuation (linear/logarithmic curves)
- Configurable min/max distance thresholds (400-4000 units)
- Real-time 3D position tracking

#### **Reverb Processing:**
- Send-based reverb with wet/dry control (0.3-0.95 mix)
- Distance-dependent reverb send levels
- Linear reverb method implementation

#### **Spatial Audio:**
- Binaural HRTF spatialization
- 3D stereo spread (200 units)
- Omni-directional sound placement
- Radius-based spatial falloff (500 units)

#### **Advanced Systems:**
- Environmental occlusion filtering
- Air absorption (frequency-dependent attenuation)
- Dynamic listener focus with azimuth scaling
- Priority-based audio culling

#### **Technical Implementation:**
- Plugin-based audio processing
- Real-time parameter interpolation
- Performance-optimized for multi-source environments
- Configurable falloff curves and attenuation shapes

### Audio Processing Pipeline

```mermaid
flowchart TD
    A[Audio Source Input] --> B{Distance Check}
    B -->|< Min Distance| C[Full Volume]
    B -->|Min-Max Range| D[Calculate Attenuation]
    B -->|> Max Distance| E[Silence/Cull]
    
    D --> F{Attenuation Method}
    F -->|Linear| G[Linear Falloff]
    F -->|Logarithmic| H[Log Falloff]
    
    G --> I[Apply Volume Attenuation]
    H --> I
    C --> I
    
    I --> J{Occlusion Check}
    J -->|Occluded| K[Apply Low-Pass Filter]
    J -->|Clear Path| L[No Occlusion Filter]
    
    K --> M[Air Absorption Processing]
    L --> M
    
    M --> N{Reverb Enabled?}
    N -->|Yes| O[Calculate Reverb Send]
    N -->|No| P[Skip Reverb]
    
    O --> Q[Apply Reverb Mix]
    Q --> R[Spatial Processing]
    P --> R
    
    R --> S{Spatialization Method}
    S -->|Binaural| T[HRTF Processing]
    S -->|3D Stereo| U[Stereo Spread]
    
    T --> V[Focus System Check]
    U --> V
    
    V --> W{Within Focus Range?}
    W -->|Yes| X[Apply Focus Scaling]
    W -->|No| Y[Standard Processing]
    
    X --> Z[Final Audio Output]
    Y --> Z
    E --> AA[Remove from Pipeline]
```

### 🦶 Dynamic Audio Effects
**Footsteps, Roars, Growls, Screams** and other audio effects are mostly baked into the animations themselves.

> **Note:** For gun-impact/hit sounds, we check material tags to play different sounds. We also have a mechanism for footsteps that changes sounds according to ground type, but it's currently disabled due to lack of quality walking sound assets.

---

## 7. 🎯 Prop Hunt Game Mode

A fun multiplayer game mode where one player is randomly selected as a prop who must hide from the other players (hunters) within a time limit.

### 🎭 Prop Player Abilities

#### 1. 🔄 Transformation System
Ability to transform into any model the prop player is contacting.

<div align="center">

![Prop Transformation System](https://github.com/th-efool/TheHollowPact/blob/main/docs/UnrealEditor_ScrIA6gnMz.gif?raw=true)

</div>

#### 2. 🪞 Decoy System  
The PropPlayer can create copies of itself to confuse, bait, and lure enemies.

<div align="center">

![Prop Decoy System](https://github.com/th-efool/TheHollowPact/blob/main/docs/comp%20(2).gif?raw=true)

</div>

---

<div align="center">

### ⭐ **Star this repository if you found it interesting!** ⭐

Made with ❤️ using Unreal Engine 5.5.4

</div>
