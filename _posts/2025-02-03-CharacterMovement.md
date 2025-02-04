---
layout: post
title: Character Movement
date: 03-02-2025
categories: [documentation]
tag: [csharp, movement]
---

# CharacterMovement Script Documentation

## Overview

The `CharacterMovement` script controls player movement, camera rotation, jumping, crouching, and sprinting in a Unity-based game. It uses `CharacterController` for physics-based movement and handles player stance changes dynamically.

----------

## Features

-   **Player Movement:** Supports walking, running, crouching, and prone movement.
-   **Camera Control:** Allows view rotation with clamped angles.
-   **Jumping and Gravity:** Implements smooth jumping mechanics.
-   **Stance Management:** Enables toggling between standing, crouching, and prone positions.
-   **Sprint Toggle:** Supports hold-to-sprint or toggle sprint.
-   **Game Manager Integration:** Can disable movement on game over.

----------

## Dependencies

-   Unity Engine (`using UnityEngine;`)
-   `CharacterController` component
-   `DefaultInput` for handling user inputs
-   `GameManager` for game state management
-   `ScriptModels` for stance settings

----------

## How It Works

### **1. Input Handling**

The script uses `DefaultInput` to get player movement and view rotation:

```csharp
private void Awake()
{
    defaultInput = new DefaultInput();
    defaultInput.Character.Movement.performed += e => input_Movement = e.ReadValue<Vector2>();
    defaultInput.Character.View.performed += e => input_View = e.ReadValue<Vector2>();
    defaultInput.Enable();
}

```

### **2. Camera View Rotation**

Rotates the camera based on mouse input while clamping the Y-axis rotation:

```csharp
private void CalculateView()
{
    newCharacterRotation.y += playerSettings.ViewXSensitivity * (playerSettings.ViewXInverted ? -input_View.x : input_View.x);
    transform.localRotation = Quaternion.Euler(newCharacterRotation);

    newCameraRotation.x += playerSettings.ViewYSensitivity * (playerSettings.ViewYInverted ? input_View.y : -input_View.y);
    newCameraRotation.x = Mathf.Clamp(newCameraRotation.x, viewClampYMin, viewClampYMax);

    cameraHolder.localRotation = Quaternion.Euler(newCameraRotation);
}

```

### **3. Player Movement**

Applies movement speed based on player input and stance:

```csharp
private void CalculateMovement()
{
    if (input_Movement.y <= 0.2f) isSprinting = false;
    var verticalSpeed = isSprinting ? playerSettings.RunningForwardSpeed : playerSettings.WalkingForwardSpeed;
    var movementSpeed = transform.TransformDirection(new Vector3(0, 0, verticalSpeed * input_Movement.y * Time.deltaTime));
    characterController.Move(movementSpeed);
}

```

### **4. Jumping Mechanism**

Checks if the player is grounded before applying jumping force:

```csharp
private void Jump()
{
    if (!characterController.isGrounded) return;
    jumpingForce = Vector3.up * playerSettings.JumpingHeight;
    playerGravity = 0;
}

```

### **5. Stance Management**

Allows toggling between different stances (Standing, Crouching, Prone):

```csharp
private void Crouch()
{
    if (playerStance == PlayerStance.Crouch) playerStance = PlayerStance.Stand;
    else playerStance = PlayerStance.Crouch;
}

```

### **6. Sprinting**

Toggles sprinting based on user input:

```csharp
private void ToggleSprint()
{
    if (input_Movement.y <= 0.2f) isSprinting = false;
    isSprinting = !isSprinting;
}

```

----------

## Configuration

The script exposes multiple settings that can be modified via Unity Inspector:

Setting

Description

`playerSettings.ViewXSensitivity`

Sensitivity for horizontal camera movement

`playerSettings.ViewYSensitivity`

Sensitivity for vertical camera movement

`playerSettings.WalkingForwardSpeed`

Speed when walking forward

`playerSettings.RunningForwardSpeed`

Speed when sprinting

`playerSettings.JumpingHeight`

Jump force applied when jumping

`viewClampYMin` / `viewClampYMax`

Clamps for vertical camera rotation

----------

## How to Use

1.  **Attach the Script** to the player GameObject with a `CharacterController` component.
2.  **Assign Dependencies:**
    -   Reference `cameraHolder` (Main Camera Transform)
    -   Set up `GameManager` if required
    -   Assign `PlayerSettingsModel` for movement settings
3.  **Customize Movement Settings** in the Inspector.
4.  **Run the Game** and control the character using WASD and Mouse.

----------

## Known Issues & Improvements

-   **Sliding on Slopes:** Consider adding slope friction adjustments.
-   **Head Bobbing:** Can be implemented for more realistic movement.
-   **Advanced Movement:** Adding vaulting, wall running, etc.

----------

## License

This project is licensed under the **MIT License**.

----------

## Contributions

Feel free to contribute by forking the repository and submitting pull requests!