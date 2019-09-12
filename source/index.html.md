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

Gripable Play Unity is a Unity Package developed by GripAble Ltd. 
It exposes the Gripable API through Unity, allowing developers with access to a GripAble Play device & Android tablet to start developing games.

# Setup

To get started with Gripable Play Unity, follow the steps [here](https://bitbucket.org/gripable/gripable-play-unity/src/master/README.md).

# Releases

Documentation links of specific Gripable Play Unity versions.

<aside class="warning">This documentation is for features still in development! Check out the documentation for the latest released version from the links below.</aside>

### Releases

* [1.0.0](https://gripableteam.github.io/gripable-play-unity-docs-1.0.0)

# Accessing the Gripable Plugin

<code>Gripable</code> is a static class, accessed to retrieve the main ojects needed to interface with the Gripable Play device. Currently, The Gripable Plugin supports connection of up to two Play devices and the UDP Client Manager.

## Play1

> Play1 returns a reference to the first Gripable Play device.

```csharp
GripablePlay gripablePlay = Gripable.Play1;
```

The first Gripable Play device is accessed via the static attribute <code>Play1</code>, which returns a <code>GripablePlay</code> object.

## Play2

The second Gripable Play device is accessed via the static attribute <code>Play2</code>, which returns a <code>GripablePlay</code> object.

> Play2 returns a reference to the second Gripable Play device.

```csharp
GripablePlay secondGripablePlay = Gripable.Play2;
```

## UdpManager

> Returns a reference to the UDP Client Manager.

```csharp
UdpClientManager udpManager = Gripable.UdpManager;
```

Returns a reference to the [UDP Client Manager](#using-udpclientmanager), used to send serialised data from the connected Gripable Play devices to a machine through UDP.

# Connecting and Disconnecting

In order to connect to a Gripable Play device, it must first be discovered by the Android device. This is achieved by turning on the Gripable Play and using the Android OS to scan for Bluetooth devices.
The <code>GripablePlay</code> object provides a wrapper around the underlying [Android BLE stack](https://developer.android.com/guide/topics/connectivity/bluetooth-le). 

<aside class="notice">
In the future, Bluetooth devices will be scanned automatically without the Android OS.
</aside>

## SetBluetoothDevice()

```csharp
void SetBluetoothDevice(BluetoothDeviceAndroid bluetoothDevice);
```

> Sets a Bluetooth Device to the <code>GripablePlay</code> object.

```csharp
string macAdress = "F3:FC:A9:B1:36:BD";
BluetoothDeviceAndroid bluetoothDevice = AndroidHelper.GetBluetoothDevice(macAddress);
gripablePlay.SetBluetoothDevice(bluetoothDevice);
```

Setting a Bluetooth Device, with a specified MAC address, to the <code>GripablePlay</code> object. Setting a Bluetooth Device for the first time flags the <code>GripablePlay</code> as initialized. Resetting the Bluetooth Device additional times is used to connect the <code>GripablePlay</code> object to a different Gripable Play device (i.e. with a different MAC adress).

### Query Parameters

Parameter | Description
--------- | -----------
<code>bluetoothDevice</code> | Sets <code>BluetoothDeviceAndroid</code> as the Bluetooth Device

## Connect()

```csharp
bool Connect();
```

> Returns <code>bool</code> that represents if a connection attempt has been successfully initiated.

```csharp
bool connectionInitiated = gripablePlay.Connect();
```

Initiates a connection attempt. Bluetooth connection is asynchronous and requires a listener for the <code>[OnConnected](#connection-events)</code> callback or call to the synchronous <code>[IsConnected](#isconnected)</code> function to check whether the connection attempt was successful.

### Return Values

Returns | Description
------- | -----------
<code>true</code> | Successfully triggered connection attempt
<code>false</code> | Failed to trigger connection attempt

## Disconnect()


```csharp
bool Disconnect();
```

> Returns a <code>bool</code> that represents if a disconnection attempt has been successfully initiated.

```csharp
bool disconnectionInitiated = gripablePlay.Disconnect();
```

Initiates a disconnection attempt. Bluetooth connection is asynchronous and requires a listener for the <code>[OnDisconnected](#disconnection-events)</code> callback or call to the synchronous <code>[IsDisconnected](#isdisconnected)</code> function to check whether the connection attempt was successful.

### Return Values

Returns | Description
------- | -----------
<code>true</code> | Successfully triggered disconnection attempt
</code>false</code> | Failed to trigger disconnection attempt

# Device Info

## IsInitialized()

```csharp
bool IsInitialized();
```

> Returns <code>true</code> if the <code>GripablePlay</code> object has been initialized with a Bluetooth Device.

```csharp
bool gripableIsInitialized = gripablePlay.IsInitialized();
```

Returns a <code>bool</code> that represents if the <code>GripablePlay</code> object has been initialized with a Bluetooth Device.

### Return Values

Returns | Description
------- | -----------
<code>true</code> | The device is initialised
<code>false</code> | The device is not initialised

## GetDeviceInfo()

```csharp
DeviceInfo GetDeviceInfo();

public struct DeviceInfo 
{
    public string ManufacturerName;
    public string ModelNumber;
    public string SerialNumber;
    public string HardwareRevision;
    public string FirmwareRevision;
}
```

> Returns an object containing all the device information.

```csharp
DeviceInfo deviceInfo = gripablePlay.GetDeviceInfo();
```

Returns a <code>DeviceInfo</code> object, which contains useful information about the connected device, such as manufacturer, device model or firmware version.

### Return Values

Returns | Description
------- | -----------
<code>DeviceInfo</code> | Object containing the device information
empty <code>DeviceInfo</code> | Empty object if the device is not initialized

## GetMacAddress()

```csharp
string GetMacAddress();
```

> Returns the MAC address of Bluetooth Device.

```csharp
string macAddress = gripablePlay.GetMacAddress();
```

Returns the associated MAC address of the currently set Bluetooth Device.

### Return Values

Returns | Description
------- | -----------
<code>macAddress</code> | String containing the MAC address of the current Bluetooth Device
<code>null</code> | The device is not initialized

## GetConnectionStatus()

```csharp
ConnectionStatus GetConnectionStatus();
```

> Returns the current connection status of the device.

```csharp
ConnectionStatus status = gripablePlay.GetConnectionStatus();
```

Returns an <code>enum</code> that represents the current connection status of the device as one of the following values: <code>CONNECTED</code>, <code>CONNECTING</code>, <code>DISCONNECTED</code> and <code>DISCONNECTING</code>.

### Return Values

Returns | Description
------- | -----------
<code>ConnectionStatus.CONNECTING</code> | The device is in the middle of a connection attempt
<code>ConnectionStatus.CONNECTED</code> | The device is connected
<code>ConnectionStatus.DISCONNECTING</code> | The device is in the middle of a disconnection attempt
<code>ConnectionStatus.DISCONNECTED</code> | The device is disconnected or not initialised

## IsConnected()

```csharp
bool IsConnected();
```

> Returns <code>true</code> if the device is connected.

```csharp
bool isConnected = gripablePlay.IsConnected();
```

Returns a <code>bool</code> that represents the device's current connected state. This is equivalent to checking if the <code>[ConnectionStatus](#getconnectionstatus)</code> is <code>CONNECTED</code>.

### Return Values

Returns | Description
------- | -----------
<code>true</code> | The device is connected
<code>false</code> | The device is connecting, disconnecting, disconnected or not initialised

## IsDisconnected()

```csharp
bool IsDisconnected();
```

> Returns <code>true</code> if the device is disconnected.

```csharp
bool isDisconnected = gripablePlay.IsDisconnected();
```

Returns a <code>bool</code> that represents the device's current disconnected state. This is equivalent to checking if the [ConnectionStatus](#getconnectionstatus) is <code>DISCONECTED</code>.

### Return Values

Returns | Description
------- | -----------
<code>true</code> | The device is disconnected or not initialised
<code>false</code> | The device is connecting, connected or disconnecting

# Subscriptions

Subscriptions are used to get data from the appropriate streams. 

Stream | Description
------ | -----------
<code>GripData</code> | Provides data from the grip sensor
<code>QuaternionData</code> | Provides the orientation of the device as a <code>[Quaternion](https://docs.unity3d.com/ScriptReference/Quaternion.html)</code> object and RPY data
<code>MotionData</code> | Provides Linear Acceleration, Angular Velocity and Magnetic Heading 

These can be subscribed to using individual methods or by passing a <code>SubscriptionConfig</code> object

## ConfigureSubscriptions()

```csharp
bool ConfigureSubscriptions(SubscriptionConfig subscription);
```

> Subscribes and Unsubscribes from the streams in the passed configuration. Returns <code>true</code> if successful.

```csharp
Gripable.Play1.ConfigureSubscriptions(new DataProtos.SubscriptionConfig
{
    GripData = true,
    QuaternionData = false,
    MotionData = false
});
```

Attempts to configure the subscriptions. Returns <code>true</code> if successful.
### Query Parameters

Parameter | Description
--------- | -----------
<code>subscriptionConfig</code> | DataProto containing boolean values for <code>GripData</code>, <code>QuaternionData</code> and <code>MotionData</code>


### Return Values

Returns | Description
------- | -----------
<code>true</code> | The subscription configuration was successful
<code>false</code> | The subscription configuration failed

## SubscribeToGripData()

```csharp
bool SubscribeToGripData();
```

> Attempts to subscribe to the device grip data, returns <code>true</code> if successful.

```csharp
bool subscriptionSuccessful = gripablePlay.SubscribeToGripData();
```

Attempts to subscribe to the device grip data. It returns <code>true</code> if the subscription was successful.

### Return Values

Returns | Description
------- | -----------
<code>true</code> | The subscription was successful
<code>false</code> | The subscription failed

## UnsubscribeFromGripData()

```csharp
bool UnsubscribeFromGripData();
```

> Attempts to unsubscribe from the device grip data, returns <code>true</code> if successful.

```csharp
bool unsubscriptionSuccessful = gripablePlay.UnsubscribeFromGripData();
```

Attempts to unsubscribe from the device grip data. It returns <code>true</code> if the unsubscription was successful.

### Return Values

Returns | Description
------- | -----------
<code>true</code> | The unsubscription was successful
<code>false</code> | The unsubscription failed

## IsSubscribedToGripData()

```csharp
bool IsSubscribedToGripData();
```

> Returns <code>true</code> if the device is subscribed to grip data.

```csharp
bool isSubscribed = gripablePlay.IsSubscribedToGripData();
```

Returns a <code>bool</code> that represents if the device is subscribed to grip data.

### Return Values

Returns | Description
------- | -----------
<code>true</code> | The device is subscribed to Grip Data
<code>false</code> | The device is not subscribed to Grip Data or not initialised

## SubscribeToQuaternionData()

```csharp
bool SubscribeToQuaternionData();
```

> Attempts to subscribe the device to quaternion data, returns <code>true</code> if successful.

```csharp
bool subscriptionSuccessful = gripablePlay.SubscribeToQuaternionData();
```

Attempts to subscribe to the device quaternion data. It returns <code>true</code> if the subscription was successful.

### Return Values

Returns | Description
------- | -----------
<code>true</code> | The subscription was successful
<code>false</code> | The subscription failed

## UnsubscribeFromQuaternionData()

```csharp
bool UnsubscribeFromQuaternionData();
```

> Attempts to unsubscribe from the device quaternion data, returns <code>true</code> if successful.

```csharp
bool unsubscriptionSuccessful = gripablePlay.UnsubscribeFromQuaternionData();
```

Attempts to unsubscribe from the device quaternion data. It returns <code>true</code> if the unsubscription was successful.

### Return Values

Returns | Description
------- | -----------
<code>true</code> | The unsubscription was successful
<code>false</code> | The unsubscription failed

## IsSubscribedToQuaternionData()

```csharp
bool IsSubscribedToQuaternionData();
```

> Returns <code>true</code> if the device is subscribed to quaternion data.

```csharp
bool isSubscribed = gripablePlay.IsSubscribedToQuaternionData();
```

Returns a <code>bool</code> that represents if the device is subscribed to quaternion data.

### Return Values

Returns | Description
------- | -----------
<code>true</code> | The device is subscribed to Quaternion Data
<code>false</code> | The device is not subscribed to Quaternion Data or is not initialised

## SubscribeToMotionData()

```csharp
bool SubscribeToMotionData();
```

> Attempts to subscribe to the device motion data, returns <code>true</code> if successful.

```csharp
bool subscriptionSuccessful = gripablePlay.SubscribeToMotionData();
```

Attempts to subscribe to the device motion data. It returns <code>true</code> if the subscription was successful.

### Return Values

Returns | Description
------- | -----------
<code>true</code> | The subscription was successful
<code>false</code> | The subscription failed

## UnsubscribeFromMotionData()

```csharp
bool UnsubscribeFromMotionData();
```

> Attempts to unsubscribe from the device motion data, returns <code>true</code> if successful.

```csharp
bool unsubscriptionSuccessful = gripablePlay.UnsubscribeFromMotionData();
```

Attempts to unsubscribe from the device motion data. It returns <code>true</code> if the unsubscription was successful.

### Return Values

Returns | Description
------- | -----------
<code>true</code> | The unsubscription was successful
<code>false</code> | The unsubscription failed

## IsSubscribedToMotionData()

```csharp
bool IsSubscribedToMotionData();
```

> Returns <code>true</code> if the device is subscribed to motion data.

```csharp
bool isSubscribed = gripablePlay.IsSubscribedToMotionData();
```

Returns a <code>bool</code> that represents if the device is subscribed to motion data.

### Return Values

Returns | Description
------- | -----------
<code>true</code> | The device is subscribed to Motion Data
<code>false</code> | The device is not subscribed to Motion Data or not initialised

# Grip Data

<code>GripData</code> contains the sensor information from squeezing the Gripable Play device.
All of these methods require subscription to <code>GripData</code>.

## GetGripData()

```csharp
GripData GetGripData();

public sealed partial class GripData : pb::IMessage<GripData>
{
    public int Timestamp;
    public float Force;
    public float CenterOfPressure;
    ...
}
```

> Returns a protobuf containing data from the grip sensor.

```csharp
GripData gripData = gripablePlay.GetGripData();
```

Returns a <code>GripData</code> protobuf containing data from the grip sensor, including the grip force and timestamp.

### Return Values

Returns | Description
------- | -----------
<code>GripData</code> | Protobuf containing the grip data
empty </code>GripData</code> | Empty GripData protobuf if not subscribed or not initialised

<aside class="warning">The <code>CenterOfPressure</code> attribute of <code>GripData</code> is currently not used and will be implemented in future releases.</aside>

## GetGripForce()

```csharp
float GetGripForce();
```

> Returns a <code>float</code> value for Grip Force in Kg.

```csharp
float gripForce = gripablePlay.GetGripForce();
```

Returns the force in Kg that is currently being applied to the Gripable Play device. This is equivalent to retrieving the force attribute from a <code>[GripData](#getgripdata)</code> object. It is sampled at 50Hz.

### Return Values

Returns | Min | Max | Description
------- | --- | --- | -----------
<code>gripForce</code> | <code>-129f</code> | <code>128f</code> | Returns the Grip Force in Kg as a float
<code>0f</code> | | | Returns 0 if the device is not subscribed or not initialised

## GetNormalizeGripForce()

```csharp
float GetNormalizeGripForce();
```

> Returns a <code>float</code> value of the normalized Grip Force.

```csharp 
float normalizeGripForce = gripablePlay.GetNormalizeGripForce();
```

Returns the normalized Grip Force, based on the current [Grip Calibration Level](#setgripcalibrationlevel).

### Return Values

Returns | Min | Max | Description
------- | --- | --- | -----------
<code>normalizeGripForce</code> | <code>0f</code> | <code>1f</code> | Returns the normalized Grip Force as a float
<code>0f</code> | | | Returns 0 if the device is not subscribed or not initialised


# Quaternion Data

Quaternion data contains the sensor information about the orientation of the device in 3D space, in the World Frame.
All of these methods require subscription to <code>QuaternionData</code>.

## GetQuaternion()

```csharp
Quaternion GetQuaternion();
```

> Returns a <code>Quaternion</code> struct representing the orientation of the device in the world frame.

```csharp
Quaternion deviceQuaternion = gripablePlay.GetQuaternion();
```

Returns a <code>[Quaternion](https://docs.unity3d.com/ScriptReference/Quaternion.html)</code> object representing the orientation of the device in the world frame.

### Return Values

Returns | Description
------- | -----------
<code>deviceQuaternion</code> | The device orientation in the world frame expressed through a [Quaternion](https://docs.unity3d.com/ScriptReference/Quaternion.html) object
<code>[Quaternion.identity](https://docs.unity3d.com/ScriptReference/Quaternion-identity.html)</code> | Returns the identity <code>[Quaternion](https://docs.unity3d.com/ScriptReference/Quaternion.html) (x=0 , y=0 , z=0, w=1)</code> if the device is not subscribed or not initialised

## GetWristRpy()

Wrist RPY (Roll, Pitch, Yaw) contains information about the orientation of the device relative to the user's wrist. [Roll, Pitch and Yaw](https://en.wikipedia.org/wiki/Aircraft_principal_axes) represent rotations of the device around its X, Y and Z axis, respectively, as shown in this [picture](#sensor-frame) (not to be mistaken with [Euler angles](https://en.wikipedia.org/wiki/Euler_angles)).

```csharp
WristRpyData GetWristRpy();

public sealed partial class WristRpyData : pb::IMessage<WristRpyData>
{
    public float Roll;
    public float Pitch;
    public float Yaw;
    ...
}
```

> Returns a protobuf containing RPY orientation data.

```csharp
WristRpyData rpyData = gripablePlay.GetWristRpyData();
```

Returns a <code>WristRpyData</code> protobuf representing the device's current orientation as Roll, Pitch and Yaw values.

### Return Values

Returns | Description
------- | -----------
<code>WristRpyData</code> | Protobuf containing the Wrist RPY data
empty </code>WristRpyData</code> | Empty WristRpyData protobuf if not subscribed or not initialised


## GetWristRpyAsVector3()

```csharp
Vector3 GetWristRpyAsVector3();
```

> Returns a <code>Vector3</code> object representing the orientation of the device as RPY.

```csharp
Vector3 rollPitchYaw = gripablePlay.GetWristRpyAsVector3();
```

Returns a <code>[Vector3](https://docs.unity3d.com/ScriptReference/Vector3.html)</code> object representing the current Roll, Pitch and Yaw of the device.


### Return Values

Returns | Min (per axis) | Max (per axis) | Description
------- | -------------- | -------------- | -----------
<code>Roll/Pitch/Yaw</code> | 0 | 360 | The device roll, pitch and yaw expressed through a <code>[Vector3](https://docs.unity3d.com/ScriptReference/Vector3.html)</code> object
<code>[Vector3.zero](https://docs.unity3d.com/ScriptReference/Vector3-zero.html)</code> | | | Returns a zeroed <code>[Vector3](https://docs.unity3d.com/ScriptReference/Vector3.html)</code> if the device is not subscribed or not initialised

## GetNormalizeRpyAsVector3()

```csharp 
Vector3 GetNormalizeRpyAsVector3();
```

> Returns a <code>Vector3</code> object representing the normalized orientation of the device as RPY.

```csharp
Vector3 normalizedRpy = gripablePlay.GetNormalizeRpyAsVector3();
```

Returns a <code>[Vector3](https://docs.unity3d.com/ScriptReference/Vector3.html)</code> object representing the normalized Roll, Pitch and Yaw values of the device.
This is calculated based on the current [RPY Calibration Level](#setrpycalibrationlevel). 

### Return Values

Returns | Min (per axis) | Max (per axis) | Description
------- | -------------- | -------------- | -----------
<code>Roll/Pitch/Yaw</code> | 0 | 1 | The device's normalized roll, pitch and yaw expressed through a <code>[Vector3](https://docs.unity3d.com/ScriptReference/Vector3.html)</code> object
<code>[Vector3.zero](https://docs.unity3d.com/ScriptReference/Vector3-zero.html)</code> | | | Returns a zeroed <code>[Vector3](https://docs.unity3d.com/ScriptReference/Vector3.html)</code> if the device is not subscribed or not initialised


# Motion Data

Motion Data contains the sensor information about the movements of the device, collected from the [gyroscope](https://en.wikipedia.org/wiki/Gyroscope), [accelerometer](https://en.wikipedia.org/wiki/Accelerometer) and [magnetometer](https://en.wikipedia.org/wiki/Magnetometer).
All of these methods require subscription to <code>MoitonData</code>.

## GetLinearAcceleration()

```csharp
Vector3 GetLinearAcceleration();
```

> Returns a <code>Vector3</code> object representing the linear acceleration of the device.

```csharp
Vector3 linearAcceleration = gripablePlay.GetLinearAcceleration();
```

Returns a <code>[Vector3](https://docs.unity3d.com/ScriptReference/Vector3.html)</code> object representing the linear acceleration of the device alongside its axes, expressed in G.

### Return Values

Returns | Min (per axis) | Max (per axis) | Description
------- | -------------- | -------------- | -----------
<code>linearAcceleration</code> | <code>-16f</code> | <code>16f</code> | A <code>[Vector3](https://docs.unity3d.com/ScriptReference/Vector3.html)</code> representing the linear acceleration of the device, expressed in G
<code>[Vector3.zero](https://docs.unity3d.com/ScriptReference/Vector3-zero.html)</code> | | | Returns a zeroed <code>[Vector3](https://docs.unity3d.com/ScriptReference/Vector3.html)</code> if the device is not subscribed or not initialised

## GetAngularVelocity()

```csharp
Vector3 GetAngularVelocity();
```

> Returns a <code>Vector3</code> object representing the angular velocity of the device.

```csharp
Vector3 angularVelocity = gripablePlay.GetAngularVelocity();
```

Returns a <code>[Vector3](https://docs.unity3d.com/ScriptReference/Vector3.html)</code> object representing the angular velocity of the device alongside its axes, expressed in degrees/sec.

### Return Values

Returns | Min (per axis) | Max (per axis) | Description
------- | -------------- | -------------- | -----------
<code>angularVelocity</code> | <code>-200f</code> | <code>200f</code> | A <code>[Vector3](https://docs.unity3d.com/ScriptReference/Vector3.html)</code> representing the angular velocity of the device, expressed in degrees/sec
<code>[Vector3.zero](https://docs.unity3d.com/ScriptReference/Vector3-zero.html)</code> | | | Returns a zeroed <code>[Vector3](https://docs.unity3d.com/ScriptReference/Vector3.html)</code> if the device is not subscribed or not initialized


## GetMagneticHeading()

```csharp
Vector3 GetMagneticHeading();
```

> Returns a <code>Vector3</code> object representing the magnetic heading of the device.

```csharp
Vector3 magneticHeading = gripablePlay.GetMagneticHeading();
```

Returns a <code>[Vector3](https://docs.unity3d.com/ScriptReference/Vector3.html)</code> object representing the magnetic heading of the device alongside its axes, expressed in microtesla.

### Return Values

Returns | Min (per axis) | Max (per axis) | Description
------- | -------------- | -------------- | -----------
<code>magneticHeading</code> | <code>-4912f</code> | <code>4912f</code> | A <code>[Vector3](https://docs.unity3d.com/ScriptReference/Vector3.html) representing the magnetic heading of the device, expressed in microteslas
<code>[Vector3.zero](https://docs.unity3d.com/ScriptReference/Vector3-zero.html)</code> | | | Returns a zeroed <code>[Vector3](https://docs.unity3d.com/ScriptReference/Vector3.html)</code> if the device is not subscribed or not initialised

# Zeroing

It is important that a Gripable Play Device is zeroed before entering into a game. This ensures that the game is playable regardless of the orientation of the device's starting position.

## ResetWristRpy()

```csharp
void ResetWristRpy();
```

> Zeroes the RPY values on the device 

```csharp
gripablePlay.ResetWristRpy();
```

Sets the Roll, Pitch and Yaw outputs of the device to zero. 


## ResetWristRpyByGravity()

```csharp 
void ResetWristRpyByGravity();
```

> Zeroes the RPY values of the device and sets it's orientation

```csharp
gripablePlay.ResetWristRpyByGravity();
```

[Motion Data](#motiondata) must be subscribed to in order for this to function. This sets the Roll, Pitch and Yaw values of the device to zero. It also calculates the orientation of the device, inverting the axes accordingly so that inputs to the games are not reversed when the Gripable Play Device is upside-down.


## ShowSqueezeToStartDialogue()

```csharp
SqueezeToStartDialogue ShowSqueezeToStartDialogue(bool resetWristRpy = false);
```

> Shows a squeeze to start dialogue canvas prefab in the game UI and returns a <code>ShowSqueezeToStartDialogue</code> script attached to that dialogue

```csharp
SqueezeToStartDialogue squeezeToStartDialogue = gripablePlay.ShowResetRpyDialogue();
squeezeToStartDialogue.OnClose += DoSomethingWhenDialogueIsClosed();
```

Shows a dialogue canvas game object to the player prompting them to squeeze. This will close the dialogue with a callback (to start the game). If the <code>resetWristRpy</code> parameter is set to <code>true</code>, the <code>[ResetWristRpyByGravity()](#resetwristrpybygravity)</code> function is also called, subscribing to [Motion Data](#motiondata) in the process.
The function returns a reference to the <code>SqueezeToStartDialogue</code> script that is attached to the dialogue game object. This can be used to subscribe to the <code>OnClose</code> action that's invoked when the dialogue closes.

### Query Parameters
Parameter | Description
--------- | -----------
<code>resetWristRpy</code> | optional <code>bool</code> which will trigger the <code>[ResetWristRpyByGravity()](#resetwristrpybygravity)</code> function when the dialogue closes

# Connection Events

> To subscribe a handler to an event, assign a reference to an encapsulated method which has no parameters and no return value.

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

 <code>GripablePlay</code> has a series of [Action Delegate](https://docs.microsoft.com/en-us/dotnet/api/system.action?view=netframework-4.7.2) connection events, which are fired at different stages of the connection process.


Callback Name | Triggered
--------- | -------
<code>OnConnected</code> | Device has been successfully connected and is ready.
<code>OnDisconnect</code> | Device has been fully disconnected.
<code>OnConnecting</code> | Device is attempting to connect.
<code>OnDisconnecting</code> | Device is attempting to disconnect. 

# Calibration

> <code>CalibrationLevel</code> enum corresponds to preset calibration levels

```csharp 
public enum CalibrationLevel
{
    LOW = 0,
    MEDIUM = 1,
    HIGH = 2
}
```

Calibration is used for calculating normalized grip and RPY functions. Force and Rotation Gestures can also be configured based on the set CalibrationLevel.

<aside class="notice">
In the future, Calibration will be calculated based on inputs from the User.
</aside>

## SetGripCalibrationLevel()

```csharp
void SetGripCalibrationLevel(CalibrationLevel level);
```

```csharp
gripablePlay.SetGripCalibrationLevel(CalibrationLevel.MEDIUM);
```

Sets the calibration level of the device for normalizing Grip Force.

### Query Parameters

Parameter | Description
--------- | -----------
<code>level</code> | Sets <code>CalibrationLevel</code> for Grip Force


## SetRpyCalibrationLevel()

```csharp
void SetRpyCalibrationLevel(CalibrationLevel level);
```

```csharp
gripablePlay.SetRpyCalibrationLevel(CalibrationLevel.MEDIUM);
```

Sets the calibration for all RPY axes of the Gripable Play Device. Used for normalize RPY functions. Can be set separately (see below).

### Query Parameters

Parameter | Description
--------- | -----------
<code>level</code> | Sets the same <code>CalibrationLevel</code> for Roll, Pitch and Yaw.


## Individual RPY Calibration

```csharp
void SetRollCalibrationLevel(CalibrationLevel level);
void SetPitchCalibrationLevel(CalibrationLevel level);
void SetYawCalibrationLevel(CalibrationLevel level);
```

```csharp
gripablePlay.SetRollCalibrationLevel(CalibrationLevel.LOW);
gripablePlay.SetPitchCalibrationLevel(CalibrationLevel.MEDIUM);
gripablePlay.SetYawCalibrationLevel(CalibrationLevel.HIGH);
```

Sets the calibration for individual RPY axes 

### Query Parameters

Parameter | Description
--------- | -----------
<code>level</code> | Sets the same <code>CalibrationLevel</code> for Roll/Pitch/Yaw.


# Gestures

> To subscribe a handler to an event, assign a reference to an encapsulated method which has no parameters and no return value.

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

 <code>GripablePlay</code> has a series of [Action Delegate](https://docs.microsoft.com/en-us/dotnet/api/system.action?view=netframework-4.7.2) gesture events, which are triggered when the Gripable detects specific, predefined movements. In particular, these gesture events divide into Force Gestures and Rotational Gestures.

## Force Gestures Callbacks

Force Gestures are triggered when interacting with the finger bar on the Gripable Play, for example when squeezing or releasing the finger bar.

Callback Name | Triggered
--------- | -------
<code>OnSqueeze</code> | When the device is squeezed, more specifically when the Grip Force reading goes from a <code>MIN_THRESHOLD</code> to a <code>MAX_THRESHOLD</code> within a given time period.
<code>OnRelease</code> | When the device is released, more specifically when the Grip Force reading goes from a <code>MAX_THRESHOLD</code> to a <code>MIN_THRESHOLD</code> within a given time period.
<code>OnDoubleSqueeze</code> | When two squeeze events occur within a time threshold.
<code>OnDoubleRelease</code> | When two release events occur within a time threshold.

## Rotation Gestures Callbacks

Rotation Gestures are triggered when the user rotates the Gripable Play device through two specified angular regions within a specified time threshold. Currently, all regions are defined by two angles around a specific axis in the [sensor frame](#sensor-frame).

Callback Name | Triggered
--------- | -------
<code>OnSupination</code> | When the device is rotated around the X axis in the positive direction. 
<code>OnPronation</code> | When the device is rotated around the X axis in the negative direction. 
<code>OnUlnar</code> | When the device is rotated around the Y axis in the positive direction. 
<code>OnRadial</code> | When the device is rotated around the Y axis in the negative direction. 
<code>OnExtension</code> | When the device is rotated around the Z axis in the positive direction. 
<code>OnFlexion</code> | When the device is rotated around the Z axis in the negative direction.
<code>OnNeutralRoll</code> | When the device's X rotation has returned to its starting position.
<code>OnNeutralPitch</code> | When the device's Y rotation has returned to its starting position.
<code>OnNeutralYaw</code> | When the device's Z rotation has returned to its starting position.

## Gesture Types

```csharp
enum GestureType
{
    SQUEEZE,
    RELEASE,
    FLEXION,
    EXTENSION,
    PRONATION,
    SUPINATION,
    ULNAR,
    RADIAL,
    NEUTRAL_YAW,
    NEUTRAL_ROLL,
    NEUTRAL_PITCH,
    DOUBLE_SQUEEZE,
    DOUBLE_RELEASE
}
```

To configure gestures, the desired gesture type must be specified. The list of available gestures is defined by the <code>GestureType enum</code>.

## GetForceGestureConfig()

```csharp
ForceConfig GetForceGestureConfig(GestureType gestureType);

public sealed partial class ForceConfig : pb::IMessage<ForceConfig>
{
    public float ReleaseThreshold;
    public float SqueezeThreshold;
    public int TimeThreshold;
    ...
}
```

> Returns a protobuf containing the configuration of the passed force gesture.

```csharp
ForceConfig forceConfig = gripablePlay.GetForceGestureConfig(GestureType.SQUEEZE);
```

Returns a <code>ForceConfig</code> protobuf containing the configuration of the specified force gesture.

### Query Parameters and Return Values

Parameter | Returns | Description
--------- | ------- | -----------
<code>gestureType</code> | <code>ForceConfig</code> | Protobuf containing the force gesture configuration of the specified force gesture type

## GetRotationGestureConfig()

```csharp
RotationConfig GetRotationGestureConfig(GestureType gestureType);

public sealed partial class RotationConfig : pb::IMessage<RotationConfig>
{
    public RegionConfig RegionA;
    public RegionConfig RegionB;
    public int TimeThreshold;
    ...
}

public sealed partial class RegionConfig : pb::IMessage<RegionConfig>
{
    public float Start;
    public float End;
    ...
}
```

> Returns a protobuf containing the configuration of the passed rotation gesture.

```csharp
RotationConfig rotationConfig = gripablePlay.GetRotationGestureConfig(GestureType.EXTENSION);
```

Returns a <code>RotationConfig</code> protobuf containing the configuration of the specified rotation gesture.

### Query Parameters and Return Values

Parameter | Returns | Description
--------- | ------- | -----------
<code>gestureType</code> | <code>ForceConfig</code> | Protobuf containing the configuration of the specified rotation gesture

## GetGestureTimeThreshold() 

```csharp
int GetGestureTimeThreshold(GestureType gestureType);
```

> Returns the time threshold of the specified gesture as an <code>int</code>.

```csharp
int timeThreshold = gripablePlay.GetTimeThreshold(GestureType.FLEXION);
```

Returns the time threshold of the specified gesture as an <code>int</code>.


### Query Parameters and Return Values

Parameter | Returns | Description
--------- | ------- | -----------
<code>gestureType</code> | <code>int</code> | time threshold of specified gesture



## ConfigureForceGesture()

```csharp
bool ConfigureForceGesture(
  GestureType gestureType,
  float releaseThreshold,
  float squeezeThreshold,
  int timeThreshold
);
```

> Configures the force and time thresholds for the specified force gesture.

```csharp
GripablePlay gp = Gripable.Play1;
float releaseForce = 0.5f;
float squeezeForce = 1f;
int timeThreshold = 5000;

gp.ConfigureForceGesture(
  GestureType.SQUEEZE,
  releaseForce, 
  squeezeForce, 
  timeThreshold
);
```

Configures the force and time thresholds for the specified force gesture. In the example, the squeeze gesture is configured to trigger when 0.5 Kg to at least 1 Kg of pressure force is detected on the finger bar, within 5 seconds.

### Query Parameters and Return Values

Parameter | Returns | Description
--------- | ------- | -----------
<code>gestureType</code>, <code>releaseThreshold</code>, <code>squeezeThreshold</code>, <code>timeThreshold</code> | <code>true</code> | Configures the gesture with the specified <code>GestureType</code> with the specified release and squeeze force thresholds (<code>float</code>) and time threshold (<code>int</code>), returns <code>true</code> if the configuration succeeded
<code>gestureType</code>, <code>releaseThreshold</code>, <code>squeezeThreshold</code>, <code>timeThreshold</code> | <code>false</code> | Configures the gesture with the specified <code>GestureType</code> with the specified release and squeeze force thresholds (<code>float</code>) and time threshold (<code>int</code>), returns <code>false</code> if the configuration was incorrect

## ConfigureRotationGesture()

```csharp
bool ConfigureRotationGesture(
  GestureType gestureType,
  Region regionA,
  Region regionB,
  int timeThreshold
);
```

> Configures the regions and the time threshold for the specified rotation gesture.

```csharp
GripablePlay gp = Gripable.Play1;
Region regionA = new Region(350, 10);
Region regionB = new Region(60, 90);
int timeThreshold = 5000;

gp.ConfigureRotationGesture(
    GestureType.EXTENSION,
    regionA,
    regionB,
    timeThreshold
);
```

Configuring a gesture with ConfigureRotationGesture requires a start region, end region and timeThreshold. 
Each region is defined by a start and end angle. An angle is considered in the region if it exists between the start and end angle, traced anti-clockwise around a circle.

In the example, the extension gesture is configured such that the start region (neutral position in this case) is set from 350 and 10 degrees (including 0 degrees), and the end region (extended position) is set from 60 to 90 degrees. If the Gripable Play device's pitch changes from a value within the start region to a value in the end region in less than the time threshold, the OnExtension event is triggered and any attached handlers are called.

### Query Parameters and Return Values

Parameter | Returns | Description
--------- | ------- | -----------
 <code>gestureType</code>, <code>regionA</code>, <code>regionB</code>, <code>timeThreshold</code> | <code>true</code> | Configures the gesture with the specified <code>GestureType</code> with the specified start and end regions (<code>Region</code>) and time threshold (<code>int</code>), returns <code>true</code> if the configuration succeeds
 <code>gestureType</code>, <code>regionA</code>, <code>regionB</code>, <code>timeThreshold</code> | <code>false</code> | Configures the gesture with the specified <code>GestureType</code> with the specified start and end regions (<code>Regions</code>) and time threshold (<code>int</code>), returns <code>false</code> if the configuration was incorrect

## ConfigureForceGesturesByCalibration()

```csharp
void ConfigureForceGesturesByCalibration();
```

```csharp
gripablePlay.ConfigureForceGesturesByCalibration();
```

Configures Squeeze, Release, Double Squeeze and Double Release gestures based on Grip Force <code>CalibrationLevel</code> preset

## ConfigureRotationGesturesByCalibration()

```csharp
void ConfigureRotationGesturesByCalibration();
```

```csharp
gripablePlay.ConfigureRotationGesturesByCalibration();
```

Configures Pronation, Supination, Radial, Ulnar, Extension and Flexion gestures based on RPY <code>CalibrationLevel</code> presets


## TriggerGesture()

```csharp
void TriggerGesture(GestureType gesture)
```

> Triggers the gesture passed as a parameter

```csharp
gripablePlay.TriggerGesture(GestureType.SQUEEZE)
```

Used to test firing gesture events without using the Gripable Device. 

### Query Parameters

Parameter | Description
--------- | -----------
<code>gestureType</code> | Triggers the gesture with the specified <code>GestureType</code>

# Vibration

## SendRumble() 

```csharp
bool SendRumbleCommand(VibrationEffect vibrationEffect);
```

> Sends a command to the Gripable Play Device to vibrate with a particular effect

```csharp
bool hasVibrated = gripablePlay.SendRumbleCommand(VibrationEffect.STRONG_CLICK_80);
```

Instructs the Gripable Play Device to vibrate with the specified vibration effect.


# Frames of Reference

Two different frames of reference can be used when working with the deice position and orientation: world frame and sensor frame.

## World Frame

The world frame of reference is the static frame of reference, independent of the device orientation.

![World frame animated](potato-world-frame.gif)

## Sensor Frame

The sensor frame is the frame relative to the device itself.

![Sensor frame animated](potato-local-frame.gif)
![Sensor frame with hand](xyz.png)

# Using UdpClientManager

The <code>UdpClientManager</code> class sends serialised data from the connected Gripable Play device to a machine via UDP. It is accessed via the <code>[UdpManager](#udpmanager)</code> static attribute of the Gripable plugin.

## StartClient(ipAddress, port)

```csharp
void StartClient(string ipAddress, int port);
```

> Starts sending UDP packets to the specified IP address and port.

```csharp
string ipAddress = "10.0.145.3";
int port = 8088;
udpManager.StartClient(ipAddress, port);
```

Starts sending packets of serialised data from all the connected Gripable Play devices to the specified IP address and port via UDP.

### Query Parameters

Parameters | Description
--------- | -----------
 <code>ipAddress</code>, <code>port</code> | Starts the client with the specified IP address (<code>string</code>) and port (<code>int</code>)

## StopClient()

```csharp
void StopClient();
```

> Stops sending UDP packets.

```csharp
udpManager.StopClient();
```

Stops sending UDP packets to the IP address and port specified in the <code>UdpClientManager</code>, if started.

### Query Parameters

Parameters | Description
--------- | -----------
<code>none</code> | Stops the UDP Client

## IsStarted()

```csharp
bool IsStarted();
```

> Returns <code>true</code> if the UDP Client is currently sending packets.

```csharp
bool udpStarted = udpManager.IsStarted();
```

Returns a <code>bool</code> that represents if the UDP Client is currently sending packets.

### Return Values

Returns | Description
------- | -----------
<code>true</code> | The UDP Client is currently sending packets
<code>false</code> | The UDP Client is currently stopped

# The Test View

The Test View is an optional UI tool used to show the data coming from each connected Gripable Play, as well as allowing basic actions to be performed on them. It is responsive to different screen layouts and resolutions and updates automatically every time the screen orientation changes (landscape/portrait mode).

## Structure

The Test View shows data and allows actions to be performed on two Gripable Plays at the same time, corresponing to the <code>Gripable.Play1</code> and <code>Gripable.Play2</code> objects.

For each of the connected Gripable Plays, several tabs are available in the Test View, grouping together data and actions of similar nature.

In order to open the Test View for one of the two Gripable Plays, the corresponing button on the left (top on portrait mode) must be selected. Similarly, tabs for a specific Gripable Play can be navigated through the buttons on the left (bottom on portrait mode), as shown in the picture below:

![Test View buttons](testview-buttons.png)

The tabs available in the Test View are as follows:

* **General tab** shows the basic device info, allows for the connection/disconnection of the Gripable Play and to start/stop the UDP Client.
* **Grip Data tab** shows data from the grip sensor, as well as allowing subscription.
* **World Frame Data tab** shows orientation data in the world frame, as well as allowing subscription.
* **Sensor Frame Data tab** shows orientation data in the sensor frame, as well as allowing subscription.
* **Motion Data tab** shows all motion data coming from the sensors (accelerometer, gyroscope and magnetometer), as well as allowing subscription.
* **Force Gestures tab** shows a trigger count for the force gestures, as well as allowing their threshold configuration.
* **Roll Gestures tab**, **Pitch Gestures tab** and **Yaw Gestures tab** show a trigger count for the rotation gestures on the three axes, as well as allowing their threshold configuration.

## Usage

In order to use the Test View into a project, the <code>TestView</code> prefab (found inside <code>GripablePlayResources/TestView/</code>) must be dragged inside the scene.

This will open up the Test View UI at runtime. The Test View can be closed through the X button on the top left and re-opened by touching anywhere on the screen with at least three fingers at the same time. Once a Gripable Play is connected through the *Connection panel* in the *General tab*, all other panels can be used for that Gripable Play.

If the project makes use of just one Gripable Play and does not need the Test View for both Gripable Plays, a Test View for just one Gripable Play can be used instead. In order to do so, instead of the <code>TestView</code> prefab, the <code>PlayView</code> prefab must be dragged into the scene. In this scenario, the <code>PlayView</code> GameObject must also be flagged as the root Test View and the Gripable Play source (<code>Gripable.Play1</code> or <code>Gripable.Play2</code>) must be specified from the inspector (both options can be found under the *TestView settings* section). In the picture below, a Play View for <code>Gripable.Play1</code> is set in the scene:

![Test View settings for a single Gripable Play](testview-playview-settings.png)

The **Connection panel** and the **UDP Client panel** in the *General tab* can be configured from the inspector in order to have a default MAC address and IP address and port, respectively. This can be done by expanding the hierarchy of the <code>TestView</code> GameObject in the scene until the GameObjects <code>ConnectionPanel</code> and <code>UdpClientPanel</code> (under <code>GeneralTab</code> and in the first and second rows, respectively), for each <code>PlayView</code> GameObject, as shown in the pictures below.

![Test View default MAC address](testview-default-mac-address.png)
![Test View default IP address & port](testview-default-ip-address-and-port.png)
