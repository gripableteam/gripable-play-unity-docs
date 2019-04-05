---
title: Gripable Play Unity

language_tabs: # must be one of https://git.io/vQNgJ
  - C#

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:

search: true
---

# Introduction

API for communicating with a Gripable Play device over Bluetooth

# Setup

To get started with Gripable Play Unity follow the steps [here](https://bitbucket.org/gripable/gripable-play-unity/src/master/README.md)

# Connecting and Disconnecting

Gripable Play provides a wrapper around the underlying [Android BLE stack](https://developer.android.com/guide/topics/connectivity/bluetooth-le). In order to connect to a Gripable Play, it must first be discovered by the Android device. This is achieved by turning on the Gripable Play and using the Android OS to scan for Bluetooth devices.

<aside class="notice">
In the future, Bluetooth devices will be scanned automatically without the Android OS.
</aside>

## Connect


```csharp
bool Connect();
```

> Connect returns true or false depending of whether a connection attempt has been successfully initiated.

```csharp
bool connectionInitiated = gripablePlay.Connect();
```

Calling Connect will initiate a connection attempt. Bluetooth connection is asynchronous and requires a listener for the [OnConnected](#connection-callbacks) callback or call to the synchronous [IsConnected](#IsConnected) function to check whether the connection attempt was successful.

### Query Parameters and Return Values

Parameter | Returns | Description
--------- | ------- | -----------
none | true | Failed to connect
none | false | Successfully connected

# Getting Grip and Motion Data

## GetGripForce


```csharp
float GetGripForce();
```

> GetGripForce returns an float value for Grip Force in Kg;

```csharp
float gripForce = gripablePlay.GetGripForce();
```

GetGripForce returns the force in Kg that is currently being applied to the Gripable. It is sampled at 50Hz.

### Query Parameters and Return Values

Parameter | Default | Min | Max | Description
--------- | ------- | --- | --- | -----------
none | 0f | -129f | 128f | Returns the Grip Force in Kg as a float



# Connection Events

> To subscribe a handler to an event, assign a function without arguments that returns void to it

```csharp
class MyView : MonoBehaviour {

  private GripablePlay _gripablePlay;

  Awake(){
    // make sure you have a reference to your Gripable Play before trying to assign to it
    _gripablePlay.OnConnected += DoSomethingWhenTheGripableConnectes    
  }
 
  public void DoSomethingWhenTheGripableConnects(){
    Debug.Log("YAY, the Gripable has connected")
  }
}

```

Gripable Play has a series of [Action Delegate](https://docs.microsoft.com/en-us/dotnet/api/system.action?view=netframework-4.7.2) connection events, which are fired at different stages of the connection process.


Callback Name | Triggered
--------- | -------
OnConnected | Device has been successfully connected and is ready.
OnDisconnect | Device has been fully disconnected.
OnConnecting | Device is attempting to connect.
OnDisconnecting | Device is attempting to disconnect. 


# Gesture Callbacks

> To subscribe a handler to an event, assign a function without arguments that returns void to it

```csharp
class MyView : MonoBehaviour {

  private GripablePlay _gripablePlay;

  Awake(){
    // make sure you have a reference to your Gripable Play before trying to assign to it
    _gripablePlay.OnSqueeze += DoSomethingWhenTheGripableIsSqueezed  
    _gripablePlay.OnRelease += DoSomethingWhenTheGripableIsReleased  
  }
 
  public void DoSomethingWhenTheGripableIsSqueezed(){
    Debug.Log("YAY, the Gripable has been squeezed")
  }

  public void DoSomethingWhenTheGripableIsReleased(){
    Debug.Log("YAY, the Gripable has been squeezed")
  }
}

```

Gripable Play has a series of [Action Delegate](https://docs.microsoft.com/en-us/dotnet/api/system.action?view=netframework-4.7.2) gesture events, which are triggered when the Gripable detects specific, predefined movements.

![alt text](xyz.png)

Callback Name | Triggered
--------- | -------
OnSqueeze | When the device is squeezed, more specifically when the Grip Force reading goes from a MIN_THRESHOLD to a MAX_THRESHOLD within a given time period.
OnRelease | When the device is released, more specifically when the Grip Force reading goes from a MAX_THRESHOLD to a MIN_THRESHOLD within a given time period.
OnSupination | When the device is rotated around the X axis in the positive direction. 
OnPronation | When the device is rotated around the X axis in the negative direction. 
OnUlnar | When the device is rotated around the Y axis in the positive direction. 
OnRadial | When the device is rotated around the Y axis in the negative direction. 
OnExtension | When the device is rotated around the Z axis in the positive direction. 
OnFlexion | When the device is rotated around the Z axis in the negative direction.
OnNeutralRoll | When the device's X rotation has returned to its starting position.
OnNeutralPitch | When the device's Y rotation has returned to its starting position.
OnNeutralYaw | When the device's Z rotation has returned to its starting position.
