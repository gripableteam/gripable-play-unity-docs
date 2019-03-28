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

Provides an api for communicating with a Gripable Play device over bluetooth

# Setup

To get started with Gripable Play Unity follow the steps [here](https://bitbucket.org/gripable/gripable-play-unity/src/master/README.md)

# Connecting and Disconnecting

Gripable Play provides a wrapper around the underlying [Android BLE stack](https://developer.android.com/guide/topics/connectivity/bluetooth-le). In order to connect a GripablePlay you will first have to have discovered it by scanning for devices. This can acheived by simply turning on your Gripable Play and using the Android OS to scan for devices.

<aside class="notice">
Future version will scan for bluetooth devices automatically and you will no longer need to use the Android OS to scan.
</aside>

## Connect


```csharp
bool Connect();
```

> Connect return true or false depending of whether a connection attempt has been successfully initiated.

```csharp
bool connectionInitiated = gripablePlay.Connect();
```

Calling Connect will attempt to initiate a connection attempt. Bluetooth connection is async so to find out whether the the connection attempt was successfull you can either listen for the [OnConnected](#connection-callbacks) callback or call the synchronous [IsConnected](#IsConnected) function.

### Query Parameters and Return Values

Parameter | Returns | Description
--------- | ------- | -----------
none | true | Failed to initiated connection attempt
none | false | Successfully initiated connection attempt

# Getting Grip and Motion Data

## GetGripForce


```csharp
float GetGripForce();
```

> GetGripForce returns an float value for Grip Force in Kg;

```csharp
float gripForce = gripablePlay.GetGripForce();
```

GetGripForce return the force in KG that is currently being applied to the Gripable. It is sampled at 50hz.

### Query Parameters and Return Values

Parameter | Default | Min | Max | Description
--------- | ------- | --- | --- | -----------
none | 0f | -129f | 128f | Returns the Grip Force in KG as a float



# Connection Callbacks

> To add a function to the callback simply assign an zero argument function that returns void to the property

```csharp
class MyView : MonoBehaviour {

  private GripablePlay _gripablePlay;

  Awake(){
    // make sure you have a reference to your GripablePlay before trying to assign to it
    _gripablePlay.OnConnected += DoSomethingWhenTheGripableConnectes    
  }
 
  public void DoSomethingWhenTheGripableConnects(){
    Debug.Log("YAY, the Gripable has connected")
  }
}

```

GripablePlay has a series of connection callbacks that are [UnityActions](https://docs.unity3d.com/ScriptReference/Events.UnityAction.html) and are triggered at different stages of the connection lifecycle.


Callback Name | Triggered
--------- | -------
OnConnected | When the device has been successfully connected and the Gatt Services have been discovered.
OnDisoconnect | When the device has been fully disconnected and the Gatt Connection has been closed.
OnConnecting | When a Connection attempt has been successfully triggered in the underlying Android BLE layer.
OnDisconnnecting | When a Disconnection attempt has been successfully triggered in the underlying Android BLE layer.


# Gesture Callbacks

> To add a function to the callback simply assign an zero argument function that returns void to the property

```csharp
class MyView : MonoBehaviour {

  private GripablePlay _gripablePlay;

  Awake(){
    // make sure you have a reference to your GripablePlay before trying to assign to it
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

GripablePlay has a series of gesture callbacks that are [UnityActions](https://docs.unity3d.com/ScriptReference/Events.UnityAction.html) and are triggered when the Gripable in moved in predefined specfic movements.

Callback Name | Triggered
--------- | -------
OnSqueeze | When the devices is squeezed, more specifically when the Grip Force reading goes from a MIN_THRESHOLD to a MAX_THRESHOLD within a given time period.
OnRelease | When the devices is released, more specifically when the Grip Force reading goes from a MAX_THRESHOLD to a MIN_THRESHOLD within a given time period.

