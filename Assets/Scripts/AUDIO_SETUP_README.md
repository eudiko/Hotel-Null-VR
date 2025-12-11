# Audio System Setup Guide

## Overview

The audio system provides background ambience and sound effects for the VR game, including special audio-based anomalies. It uses Unity's AudioMixer for professional audio control.

## Setup Instructions

### 1. Create AudioMixer Asset

1. In Project window, right-click → **Create → Audio Mixer**
2. Name it `GameAudioMixer`
3. Open the AudioMixer window (Window → Audio → Audio Mixer)
4. In the Groups panel, create this structure:
   ```
   Master
   ├── Ambience
   └── SFX
   ```
5. Select each group and click "Add exposed parameter to..." for Volume
6. Name the exposed parameters:
   - Master group: `MasterVolume`
   - Ambience group: `AmbienceVolume`
   - SFX group: `SFXVolume`

**Quick Setup:**

- Select Master → right-click Volume → Expose 'Volume (of Master)' to script → rename to `MasterVolume`
- Select Ambience → right-click Volume → Expose 'Volume (of Ambience)' to script → rename to `AmbienceVolume`
- Select SFX → right-click Volume → Expose 'Volume (of SFX)' to script → rename to `SFXVolume`

### 2. Create AudioManager GameObject

1. In your scene, create an empty GameObject named "AudioManager"
2. Add the `AudioManager` component to it
3. The AudioManager will persist across scenes (DontDestroyOnLoad)

**OR** use the automatic setup:

1. Add `AudioManagerSetup` component to any GameObject in your scene
2. It will automatically create the AudioManager when the scene starts

### 3. Assign AudioMixer to AudioManager

In the AudioManager Inspector:

1. **Audio Mixer**: Drag your `GameAudioMixer` asset
2. **Ambience Group**: Select the Ambience group from dropdown
3. **SFX Group**: Select the SFX group from dropdown

### 4. Assign Audio Clips

In the AudioManager component, assign your audio clips:

#### Ambience Clips

- **Normal Ambience**: The default background ambience (looping)
- **Weird Ambience Clips**: Array of unsettling ambience sounds for anomalies

#### Door SFX

- **Door Open Sound**: Sound when door opens
- **Door Close Sound**: Sound when door closes
- **Door Locked Sound**: Sound when trying to open locked door

#### Player SFX

- **Footstep Sounds**: Array of footstep sounds (random selection)
- **Player Breath Sound**: Heavy breathing sound

#### Anomaly SFX

- **Creepy Sounds**: Array of creepy sound effects
- **Whisper Sounds**: Array of whisper/voice sounds
- **Distortion Sounds**: Array of distorted/glitchy sounds

### 5. Volume Settings

Adjust the volume sliders (in decibels):

- **Master Volume**: -80dB to 0dB (default: 0dB)
- **Ambience Volume**: -80dB to 0dB (default: -10dB)
- **SFX Volume**: -80dB to 0dB (default: -5dB)
- **SFX Volume**: 0-1 (default: 0.7)

## Using Audio-Based Anomalies

### HallwayChange Component - Multiple Change Types

The `HallwayChange` component now supports **multiple change configurations** on the same object:

1. Select a GameObject with a `HallwayChange` component
2. In the Inspector, expand "Change Configurations" list
3. Add multiple entries (click the + button)
4. Configure each change type:

**Visual Changes:**

- DisableObject
- EnableObject
- ChangeColor
- ChangePosition
- ChangeRotation
- ChangeScale
- SwapObject

**Audio Changes:**

- **PlayCreepySound**: Plays a random creepy sound
- **PlayWhisper**: Plays a random whisper sound
- **PlayDistortion**: Plays a random distortion sound
- **StopAmbience**: Stops the background ambience completely
- **WeirdAmbience**: Temporarily replaces ambience with weird sound (set duration)
- **FadeOutAmbience**: Gradually fades out the ambience (set duration)

### Random Selection

When a hallway with changes is spawned, **one random change** from the list will be selected and applied. This adds variety to anomalies!

### Example Configuration

For a lamp object:

```
Change Configurations (Size: 3)
├─ Element 0
│  ├─ Change Type: ChangeColor
│  └─ New Color: Red
├─ Element 1
│  ├─ Change Type: PlayCreepySound
│  └─ Audio Duration: 3
└─ Element 2
   ├─ Change Type: DisableObject
```

Each time this hallway spawns, one of these three changes will happen randomly.

## Audio Control from Code

### Playing Sounds

```csharp
// Background ambience
AudioManager.Instance.PlayNormalAmbience();
AudioManager.Instance.PlayWeirdAmbience();
AudioManager.Instance.StopAmbience();

// Temporary weird ambience (returns to normal after duration)
AudioManager.Instance.PlayRandomWeirdAmbienceTemporary(5f); // 5 seconds

// Fade effects
AudioManager.Instance.FadeOutAmbience(2f); // Fade out over 2 seconds
AudioManager.Instance.FadeInAmbience(2f);  // Fade in over 2 seconds

// Door sounds
AudioManager.Instance.PlayDoorOpen();
AudioManager.Instance.PlayDoorClose();
AudioManager.Instance.PlayDoorLocked();

// Player sounds
AudioManager.Instance.PlayFootstep();
AudioManager.Instance.PlayBreath();

// Anomaly sounds
AudioManager.Instance.PlayCreepySound();
AudioManager.Instance.PlayWhisper();
AudioManager.Instance.PlayDistortion();
AudioManager.Instance.PlayRandomAnomalySound(); // Random from all three

// Custom sound
AudioManager.Instance.PlaySFX(myAudioClip);
AudioManager.Instance.PlaySFX(myAudioClip, 0.5f); // With volume scale
```

### Volume Control

```csharp
// Set volumes (in decibels, -80 to 0)
AudioManager.Instance.SetMasterVolume(-5f);    // Slightly quieter
AudioManager.Instance.SetAmbienceVolume(-15f); // Quiet ambience
AudioManager.Instance.SetSFXVolume(0f);        // Full volume SFX

// Or modify directly
AudioManager.Instance.masterVolume = -10f;
AudioManager.Instance.ambienceVolume = -20f;
AudioManager.Instance.sfxVolume = -5f;
AudioManager.Instance.UpdateVolumes(); // Apply changes
```

### Working with AudioMixer

The AudioManager uses Unity's AudioMixer system, which provides:

- Professional audio routing and mixing
- Real-time DSP effects (reverb, lowpass, etc.)
- Snapshots for different game states
- Exposed parameters for dynamic control

You can add effects to the mixer groups:

1. Open Audio Mixer window
2. Select a group (Master, Ambience, or SFX)
3. Click "Add Effect" → choose effect (Reverb, Lowpass, etc.)
4. Adjust effect parameters for desired sound

## Door Integration

The `DoorController` automatically uses AudioManager for door sounds. You no longer need to assign AudioSource or clips directly to doors.

## Tips

- Use **StopAmbience** or **FadeOutAmbience** for really unsettling moments
- **WeirdAmbience** is great for subtle wrongness - it returns to normal automatically
- Mix audio and visual changes on the same prop for more complex anomalies
- Use multiple **PlayCreepySound** entries with different durations for variety
- Keep **Footstep Sounds** array populated with 3-5 variations for natural walking
