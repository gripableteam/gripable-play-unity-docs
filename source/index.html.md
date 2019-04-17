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

API for communicating with a Gripable Play device over Bluetooth.

# Setup

To get started with Gripable Play Unity, follow the steps [here](https://bitbucket.org/gripable/gripable-play-unity/src/master/README.md).

# Accessing the Gripable Plugin

The Gripable Plugin is statically accessed to retrieve the main ojects needed to interface with the Gripable Play device. At the moment the Gripable Plugin allows you to retrieve up to two available Gripable Play devices and the UDP Client Manager. The Gripable Plugin does not need any setup and can be called statically when needed.

## Play1

> Play1 returns a reference to the first Gripable Play device.

```csharp
GripablePlay gripablePlay = Gripable.Play1;
```

The first Gripable Play device is accessed via the static attribute Play1, which returns a <code>GripablePlay</code> object.

## Play2

The second Gripable Play device is accessed via the static attribute Play2, which returns a <code>GripablePlay</code> object.

> Play2 returns a reference to the second Gripable Play device.

```csharp
GripablePlay secondGripablePlay = Gripable.Play2;
```

## UdpManager

> Returns a reference to the UDP Client Manager.

```csharp
UdpClientManager udpManager = Gripable.UdpManager;
```

Returns a reference to the [UDP Client Manager](#using-udpclientmanager), used to send serialised data from the connected Gripable Plays to a machine through UDP.

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

Initiates a connection attempt. Bluetooth connection is asynchronous and requires a listener for the [OnConnected](#connection-events) callback or call to the synchronous [IsConnected](#isconnected) function to check whether the connection attempt was successful.

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

Initiates a disconnection attempt. Bluetooth connection is asynchronous and requires a listener for the [OnDisconnected](#disconnection-events) callback or call to the synchronous [IsDisconnected](#isdisconnected) function to check whether the connection attempt was successful.

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

> Returns true if the <code>GripablePlay</code> object has been initialized with a Bluetooth Device.

```csharp
bool gripableIsInitialized = gripablePlay.IsInitialized();
```

Returns a <code>bool</code> that represents if the <code>GripablePlay</code> object has been initialized with a Bluetooth Device.

### Return Values

Returns | Description
------- | -----------
<code>true</code> | The device is initialised
<code>false</code> | The device is not initialised

## GetDevicenfo()

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
macAddress | String containing the MAC address of the current Bluetooth Device
null | The device is not initialized

## GetConnectionStatus()

```csharp
ConnectionStatus GetConnectionStatus();
```

> Returns the current connection status of the device.

```csharp
ConnectionStatus status = gripablePlay.GetConnectionStatus();
```

Returns an enum that represents the current connection status of the device as one of the following values: <code>CONNECTED</code>, <code>CONNECTING</code>, <code>DISCONNECTED</code> and <code>DISCONNECTING</code>.

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

> Returns true if the device is connected.

```csharp
bool isConnected = gripablePlay.IsConnected();
```

Returns a <code>bool</code> that represents the device's current connected state. This is equivalent to checking if the [ConnectionStatus](#getconnectionstatus) is <code>CONNECTED</code>.

### Return Values

Returns | Description
------- | -----------
true | The device is connected
false | The device is connecting, disconnecting, disconected or not initialised

## IsDisconnected()

```csharp
bool IsDisconnected();
```

> Returns true if the device is disconnected.

```csharp
bool isDisconnected = gripablePlay.IsDisconnected();
```

Returns a <code>bool</code> that represents the device's current disconnected state. This is equivalent to checking if the [ConnectionStatus](#getconnectionstatus) is <code>DISCONECTED</code>.

### Return Values

Returns | Description
------- | -----------
<code>true</code> | The device is disconnected or not initialised
<code>false</code> | The device is connecting, connected or disconnecting

# Grip Data

<code>GripData</code> contains the sensor information from squeezing the Gripable Play device.

## SubscribeToGripData()

```csharp
bool SubscribeToGripData();
```

> Attempts to subscribe to the device grip data, returns true if successful.

```csharp
bool subscriptionSuccessful = gripablePlay.SubscribeToGripData();
```

Attempts to subscribe to the device grip data. It returns true if the subscription was successful.

### Return Values

Returns | Description
------- | -----------
<code>true</code> | The subscription was successful
<code>false</code> | The subscription failed

## UnsubscribeFromGripData()

```csharp
bool UnsubscribeFromGripData();
```

> Attempts to unsubscribe from the device grip data, returns true if successful.

```csharp
bool unsubscriptionSuccessful = gripablePlay.UnsubscribeFromGripData();
```

Attempts to unsubscribe from the device grip data. It returns true if the unsubscription was successful.

### Return Values

Returns | Description
------- | -----------
<code>true</code> | The unsubscription was successful
<code>false</code> | The unsubscription failed

## IsSubscribedToGripData()

```csharp
bool IsSubscribedToGripData();
```

> Returns true if the device is subscribed to grip data.

```csharp
bool isSubscribed = gripablePlay.IsSubscribedToGripData();
```

Returns a <code>bool</code> that represents if the device is subscribed to grip data.

### Return Values

Returns | Description
------- | -----------
<code>true</code> | The device is subscribed to Grip Data
<code>false</code> | The device is not subscribed to Grip Data or not initialised

## GetGripData()

```csharp
GripData GetGripData();

public struct GripData
{
    public int Timestamp;
    public float Force;
    public float CenterOfPressure;
}
```

> Returns an object containing data from the grip sensor.

```csharp
GripData gripData = gripablePlay.GetGripData();
```

Returns a <code>GripData</code> object containing data from the grip sensor, including the grip force and timestamp.

### Return Values

Returns | Description
------- | -----------
<code>GripData</code> | Object containing the grip data
empty </code>GripData</code> | Empty GripData object if not subscribed or not initialised

<aside class="warning">The <code>CenterOfPressure</code> attribute of <code>GripData</code> is currently not used and will be implemented in future releases.</aside>

## GetGripForce()

```csharp
float GetGripForce();
```

> Returns a float value for Grip Force in Kg.

```csharp
float gripForce = gripablePlay.GetGripForce();
```

Returns the force in Kg that is currently being applied to the Gripable Play device. This is equivalent to retrieving the force attribute from a <code>[GripData](#getgripdata)</code> object. It is sampled at 50Hz.

### Return Values

Returns | Min | Max | Description
------- | --- | --- | -----------
<code>gripForce</code> | <code>-129f</code> | <code>128f</code> | Returns the Grip Force in Kg as a float
<code>0f</code> | | | Returns 0 if the device is not subscribed or not initialised

# World Frame Quaternion

World Frame Quaternion data contains the sensor information about the orientation of the device in 3D space.

## SubscribeToWorldFrameQuaternion()

```csharp
bool SubscribeToWorldFrameQuaternion();
```

> Attempts to subscribe to the device world frame quaternion data, returns true if successful.

```csharp
bool subscriptionSuccessful = gripablePlay.SubscribeToWorldFrameQuaternion();
```

Attempts to subscribe to the device world frame quaternion data. It returns true if the subscription was successful.

### Return Values

Returns | Description
------- | -----------
<code>true</code> | The subscription was successful
<code>false</code> | The subscription failed

## UnsubscribeFromWorldFrameQuaternion()

```csharp
bool UnsubscribeFromWorldFrameQuaternion();
```

> Attempts to unsubscribe from the device world frame quaternion data, returns true if successful.

```csharp
bool unsubscriptionSuccessful = gripablePlay.UnsubscribeFromWorldFrameQuaternion();
```

Attempts to unsubscribe from the device world frame quaternion data. It returns true if the unsubscription was successful.

### Return Values

Returns | Description
------- | -----------
<code>true</code> | The unsubscription was successful
<code>false</code> | The unsubscription failed

## IsSubscribedToWorldFrameQuaternion()

```csharp
bool IsSubscribedToWorldFrameQuaternion();
```

> Returns true or false whether the device is subscribed to world frame quaternion data or not.

```csharp
bool isSubscribed = gripablePlay.IsSubscribedToWorldFrameQuaternion();
```

Returns true or false whether the device is subscribed to world frame quaternion data or not.

### Return Values

Returns | Description
------- | -----------
<code>true</code> | The device is subscribed to world frame Quaternion
<code>false</code> | The device is not subscribed to world frame Quaternion or not initialised

## GetWorldFrameQuaternion()

```csharp
Quaternion GetWorldFrameQuaternion();
```

> Returns a Quaternion object representing the orientation of the device in the world frame.

```csharp
Quaternion deviceQuaternion = gripablePlay.GetWorldFrameQuaternion();
```

Returns a [Quaternion](https://docs.unity3d.com/ScriptReference/Quaternion.html) object representing the orientation of the device in the world frame.

### Return Values

Returns | Description
------- | -----------
<code>deviceQuaternion</code> | The device orientation in the world frame expressed through a [Quaternion](https://docs.unity3d.com/ScriptReference/Quaternion.html) object
<code>[Quaternion.identity](https://docs.unity3d.com/ScriptReference/Quaternion-identity.html)</code> | Returns the identity [Quaternion](https://docs.unity3d.com/ScriptReference/Quaternion.html) (x,y,z=0, w=1) if the device is not subscribed or not initialised

# Sensor Frame RPY

Sensor frame Roll, Pitch and Yaw contain the sensor information about the orientation of the device relative to the sensors frame. [Roll, Pitch and Yaw](https://en.wikipedia.org/wiki/Aircraft_principal_axes) represent, respectively, rotations of the device around its X, Y and Z axis, as shown in this [picture](#sensor-frame).

## SubscribeToSensorFrameRPY()

```csharp
bool SubscribeToSensorFrameRPY();
```

> Attempts to subscribe to the device sensor frame RPY data, returns true if successful.

```csharp
bool subscriptionSuccessful = gripablePlay.SubscribeToSensorFrameRPY());
```

Attempts to subscribe to the device sensor frame RPY data. It returns true if the subscription was successful.

### Return Values

Returns | Description
------- | -----------
<code>true</code> | The subscription was successful
<code>false</code> | The subscription failed

## UnsubscribeFromSensorFrameRPY()

```csharp
bool UnsubscribeFromSensorFrameRPY();
```

> Attempts to unsubscribe from the device sensor frame RPY data, returns true if successful.

```csharp
bool unsubscriptionSuccessful = gripablePlay.UnsubscribeFromSensorFrameRPY();
```

Attempts to unsubscribe from the device sensor frame RPY data. It returns true if the unsubscription was successful.

### Return Values

Returns | Description
------- | -----------
<code>true</code> | The unsubscription was successful
<code>false</code> | The unsubscription failed

## IsSubscribedToSensorFrameRPY()

```csharp
bool IsSubscribedToSensorFrameRPY();
```

> Returns true or false whether the device is subscribed to sensor frame RPY data or not.

```csharp
bool isSubscribed = gripablePlay.IsSubscribedToSensorFrameRPY();
```

Returns true or false whether the device is subscribed to world sensor frame RPY data or not.

### Return Values

Returns | Description
------- | -----------
<code>true</code> | The device is subscribed to sensor frame RPY
<code>false</code> | The device is not subscribed to sensor frame RPY or not initialized

## GetSensorFrameRpy()

```csharp
Vector3 GetSensorFrameRpy();
```

> Returns a <code>Vector3</code> object representing the RPY of the device.

```csharp
Vector3 rollPitchYaw = gripablePlay.GetSensorFrameRpy();
```

Returns a <code>[Vector3](https://docs.unity3d.com/ScriptReference/Vector3.html)</code> object representing the current Roll, Pitch and Yaw of the device.

### Return Values

Returns | Description
------- | -----------
<code>rollPitchYaw</code> | The device roll, pitch and yaw expressed through a <code>[Vector3](https://docs.unity3d.com/ScriptReference/Vector3.html)</code> object
<code>[Vector3.zero](https://docs.unity3d.com/ScriptReference/Vector3-zero.html)</code> | Returns a zeroed <code>[Vector3](https://docs.unity3d.com/ScriptReference/Vector3.html)</code> if the device is not subscribed or not initialised

# Motion Data

Motion Data contains the sensor information about the movements of the device, collected from the [gyroscope](https://en.wikipedia.org/wiki/Gyroscope), [accelerometer](https://en.wikipedia.org/wiki/Accelerometer) and [magnetometer](https://en.wikipedia.org/wiki/Magnetometer).

## SubscribeToMotionData()

```csharp
bool SubscribeToMotionData();
```

> Attempts to subscribe to the device motion data, returns true if successful.

```csharp
bool subscriptionSuccessful = gripablePlay.SubscribeToMotionData();
```

Attempts to subscribe to the device motion data. It returns true if the subscription was successful.

### Return Values

Returns | Description
------- | -----------
<code>true</code> | The subscription was successful
<code>false</code> | The subscription failed

## UnsubscribeFromMotionData()

```csharp
bool UnsubscribeFromMotionData();
```

> Attempts to unsubscribe from the device motion data, returns true if successful.

```csharp
bool unsubscriptionSuccessful = gripablePlay.UnsubscribeFromMotionData();
```

Attempts to unsubscribe from the device motion data. It returns true if the unsubscription was successful.

### Return Values

Returns | Description
------- | -----------
<code>true</code> | The unsubscription was successful
<code>false | The unsubscription failed

## IsSubscribedToMotionData()

```csharp
bool IsSubscribedToMotionData();
```

> Returns true or false whether the device is subscribed to motion data or not.

```csharp
bool isSubscribed = gripablePlay.IsSubscribedToMotionData();
```

Returns true or false whether the device is subscribed to motion data or not.

### Return Values

Returns | Description
------- | -----------
<code>true</code> | The device is subscribed to Motion Data
<code>false</code> | The device is not subscribed to Motion Data or not initialised

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

Returns a <code>[Vector3](https://docs.unity3d.com/ScriptReference/Vector3.html)</code> object representing the magnetic heading of the device alongside its axes, expressed in microteslas.

### Return Values

Returns | Min (per axis) | Max (per axis) | Description
------- | -------------- | -------------- | -----------
<code>magneticHeading</code> | <code>-4912f</code> | <code>4912f</code> | A <code>[Vector3](https://docs.unity3d.com/ScriptReference/Vector3.html) representing the magnetic heading of the device, expressed in microteslas
<code>[Vector3.zero](https://docs.unity3d.com/ScriptReference/Vector3-zero.html)</code> | | | Returns a zeroed <code>[Vector3](https://docs.unity3d.com/ScriptReference/Vector3.html)</code> if the device is not subscribed or not initialised

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


# Gestures

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

Gripable Play has a series of [Action Delegate](https://docs.microsoft.com/en-us/dotnet/api/system.action?view=netframework-4.7.2) gesture events, which are triggered when the Gripable detects specific, predefined movements. In particular, these gesture events divide into Force Gestures and Rotational Gestures.

## Force Gestures Callbacks

Force Gestures are triggered when interacting with the fingerbar on the Gripable Play, for example when squeezing or releasing the fingerbar.

Callback Name | Triggered
--------- | -------
OnSqueeze | When the device is squeezed, more specifically when the Grip Force reading goes from a MIN_THRESHOLD to a MAX_THRESHOLD within a given time period.
OnRelease | When the device is released, more specifically when the Grip Force reading goes from a MAX_THRESHOLD to a MIN_THRESHOLD within a given time period.

## Rotation Gestures Callbacks

Rotation Gestures are triggered when the user rotates the Gripable Play between two specific angular regions within a specific time threshold. Currently all regions are defined by specifying two angles around a specific axis in the [sensor frame](#sensor-frame).

Callback Name | Triggered
--------- | -------
OnSupination | When the device is rotated around the X axis in the positive direction. 
OnPronation | When the device is rotated around the X axis in the negative direction. 
OnUlnar | When the device is rotated around the Y axis in the positive direction. 
OnRadial | When the device is rotated around the Y axis in the negative direction. 
OnExtension | When the device is rotated around the Z axis in the positive direction. 
OnFlexion | When the device is rotated around the Z axis in the negative direction.
OnNeutralRoll | When the device's X rotation has returned to its starting position.
OnNeutralPitch | When the device's Y rotation has returned to its starting position.
OnNeutralYaw | When the device's Z rotation has returned to its starting position.

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
    NEUTRAL_PITCH
}
```

In order to configure gestures you need to speficy the gesture type you want to configure first. The list of available gestures is defined by the GestureType enum, as shown here on the side.

## ConfigureForceGesture()

```csharp
bool ConfigureForceGesture(
  GestureType gestureType,
  float releaseThreshold,
  float squeezeThreshold,
  int timeThreshold
);
```

> ConfigureForceGesture configures the force and time thresholds for the specified force gesture.

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

ConfigureForceGesture configures the force and time thresholds for the specified force gesture. In the example shown on the side, we can see how a squeeze gesture can be configured to be triggered when going from 0.5 Kg to at least 1 Kg of pressure force on the fingerbar within 5 seconds.

### Query Parameters and Return Values

Parameter | Returns | Description
--------- | ------- | -----------
gestureType, releaseThreshold, squeezeThreshold, timeThreshold | true | Configures the gesture with the specified gestureType (GestureType) with the specified release and squeeze force thresholds (floats) and time threshold (int), returns true if the configuration succeded
gestureType, releaseThreshold, squeezeThreshold, timeThreshold | false | Configures the gesture with the specified gestureType (GestureType) with the specified release and squeeze force thresholds (floats) and time threshold (int), returns false if the configuration was incorrect

## ConfigureRotationGesture()

```csharp
bool ConfigureRotationGesture(
  GestureType gestureType,
  Region regionA,
  Region regionB,
  int timeThreshold
);
```

> ConfigureRotationGesture configures the regions and the time threshold for the specified rotation gesture.

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

In order to configure a specific gesture with ConfigureRotationGesture you need 3 things. A start region an end region and a timeThreshold. 
The region are defined by specifying a start and an end angle of rotation. A third angle is considered to be "in the region" if it is between the start angle and the end one, in anti-clockwise direction.

In the example shown on the side, we are configuring the extention gesture. The start region, i.e. the neutral position in this case, is defined from 350 and 10 degrees (i.e. including 0 degrees), while the end region, i.e. the extended position, is defined from 60 to 90 degrees. With this configuration, if the Gripable Play's pitch changes from a value inside the start region to a value within the end region in less than 5 second (the time threshold), then the OnExtension calback is invoked.

### Query Parameters and Return Values

Parameter | Returns | Description
--------- | ------- | -----------
gestureType, regionA, regionB, timeThreshold | true | Configures the gesture with the specified gestureType (GestureType) with the specified start and end regions (Regions) and time threshold (int), returns true if the configuration succeded
gestureType, regionA, regionB, timeThreshold | false | Configures the gesture with the specified gestureType (GestureType) with the specified start and end regions (Regions) and time threshold (int), returns false if the configuration was incorrect

# Frames of Reference

Two different frames of reference can be used when working with the deice position and orientation: world frame and sensor frame.

## World Frame

The world frame of reference is the static frame of reference, independent of the device orientation, of the world space where the device is located.

## Sensor Frame

The sensor frame is the frame relative to the device itself.

![alt text](xyz.png)

# Using UdpClientManager

The UdpClientManager class can be very useful to sen serialised data of the connected Gripable Plays to a machine through UDP. The UdpClientManager can be accessed via the [UdpManager static attribute](#udpmanager) of the Gripable plugin.

## StartClient(ipAddress, port)

```csharp
void StartClient(string ipAddress, int port);
```

> Starts sending udp packets to the specified IP address and port.

```csharp
string ipAddress = "10.0.145.3";
int port = 8088;
udpManager.StartClient(ipAddress, port);
```

StartClient starts sending udp packets of serialised data of all the connected gripable plays to the specified IP address and port through UDP.

### Query Parameters

Parameters | Description
--------- | -----------
ipAddress, port | Starts the client with the specified IP address (string) and port (integer)

## StopClient()

```csharp
void StopClient();
```

> Stops sending udp packets.

```csharp
udpManager.StopClient();
```

StopClient stops sending udp packets to whatever IP address and port the UdpClientManager was sending before, if started. otherwise does nothing.

### Query Parameters

Parameters | Description
--------- | -----------
none | Stops the Udp Client

## IsStarted()

```csharp
bool IsStarted();
```

> Returns true or false whether the Udp Client is currently sending packets.

```csharp
bool udpStarted = udpManager.IsStarted();
```

IsStarted returns true or false whether the Udp Client is currently sending packets.

### Return Values

Returns | Description
------- | -----------
true | The Udp Client is currently sending packets
false | The Udp Client is currently stopped
