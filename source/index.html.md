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

# Accessing the Gripable plugin

The Gripable plugin can easily be accessed in a static fashion in order to retrieve the main ojects needed to interface with the Gripable Play. At the moment the Gripable plugin allows to retrieve the two available Gripable Plays and the UDP Client Manager. the Gripable plugin does not need any setup and can be called in a static fashion whenever needed.

## Play1

> Play1 returns a reference to the first Gripable Play.

```csharp
GripablePlay gripablePlay = Gripable.Play1;
```

The first Gripable Play can be accessed through the static attribute Play1, which returns a GripablePlay object.

## Play2

The second Gripable Play can be accessed through the static attribute Play2, which returns a GripablePlay object.

> Play2 returns a reference to the second Gripable Play.

```csharp
GripablePlay secondGripablePlay = Gripable.Play2;
```

## UdpManager

> UdpManager returns a reference to the UDP Client Manager.

```csharp
UdpClientManager udpManager = Gripable.UdpManager;
```

UdpManager returns a reference to the UDP Client Manager, used to send serialised data from the connected Gripable Plays to a machine through UDP.

# Connecting and Disconnecting

Gripable Play provides a wrapper around the underlying [Android BLE stack](https://developer.android.com/guide/topics/connectivity/bluetooth-le). In order to connect to a Gripable Play, it must first be discovered by the Android device. This is achieved by turning on the Gripable Play and using the Android OS to scan for Bluetooth devices.

<aside class="notice">
In the future, Bluetooth devices will be scanned automatically without the Android OS.
</aside>

## SetBluetoothDevice(bluetoothDevice)

```csharp
void SetBluetoothDevice(BluetoothDeviceAndroid bluetoothDevice);
```

> SetBluetoothDevice sets a Bluetooth Device to the Gripable Play.

```csharp
string macAdress = "F3:FC:A9:B1:36:BD";
BluetoothDeviceAndroid bluetoothDevice = AndroidHelper.GetBluetoothDevice(macAddress);
gripablePlay.SetBluetoothDevice(bluetoothDevice);
```

SetBluetoothDevice sets a Bluetooth Device, with a specified MAC address, to the Gripable Play. Setting a Bluetooth Device for the first time flags the Gripable Play as initialised. Resetting the Bluetooth Device additional times is used to connect the Gripable Play to a different physical Gripable Play device (i.e. with a different MAC adress).

### Query Parameters

Parameter | Description
--------- | -----------
bluetoothDevice | Sets bluetoothDevice as the Bluetooth Device

## Connect()

```csharp
bool Connect();
```

> Connect returns true or false depending of whether a connection attempt has been successfully initiated.

```csharp
bool connectionInitiated = gripablePlay.Connect();
```

Calling Connect will initiate a connection attempt. Bluetooth connection is asynchronous and requires a listener for the [OnConnected](#connection-callbacks) callback or call to the synchronous [IsConnected](#IsConnected) function to check whether the connection attempt was successful.

### Return Values

Returns | Description
------- | -----------
true | Failed to connect
false | Successfully connected

## Disconnect()


```csharp
bool Disconnect();
```

> Disconnect returns true or false depending of whether a disconnection attempt has been successfully initiated.

```csharp
bool disconnectionInitiated = gripablePlay.Disconnect();
```

Calling Disconnect will initiate a disconnection attempt. Bluetooth connection is asynchronous and requires a listener for the [OnDisconnected](#disconnection-callbacks) callback or call to the synchronous [IsDisconnected](#IsDisconnected) function to check whether the connection attempt was successful.

### Return Values

Returns | Description
------- | -----------
true | Failed to disconnect
none | false | Successfully disconnected

# Getting Device Info

## IsInitialized()

```csharp
bool IsInitialized();
```

> IsInitialized returns true or false whether a Bluetooth Device have been set or not.

```csharp
bool gripableIsInitialized = gripablePlay.IsInitialized();
```

IsInitialized returns true or false whether a Bluetooth Device have been set or not. This can often be useful as most of the operations on the Gripale Play can only be executed while the Gripable Play is correctly initialised.

### Return Values

Returns | Description
------- | -----------
true | Failed to connect
false | Successfully connected

## GetDevicenfo()

```csharp
DeviceInfo GetDeviceInfo();
```

> GetDeviceInfo returns an object containing all the device information.

```csharp
DeviceInfo deviceInfo = gripablePlay.GetDeviceInfo();
```

GetDeviceInfo returns a DeviceInfo object, which contains useful information about the connected device, such as manufacturer, device model or firmware version.

### Return Values

Returns | Description
------- | -----------
deviceInfo | Returns an object containing the device information
empty DeviceInfo | Returns an empty object if the device is not initialised

## GetMacAddress()

```csharp
string GetMacAddress();
```

> GetMacAddress returns the MAC address of Bluetooth Device.

```csharp
string macAddress = gripablePlay.GetMacAddress();
```

GetMacAddress returns the associated MAC address of the currently set Bluetooth Device.

### Return Values

Returns | Description
------- | -----------
macAddress | Returns a string containing the MAC address of the current Bluetooth Device
null | Returns null if the device is not initialised

## GetConnectionStatus()

```csharp
ConnectionStatus GetConnectionStatus();
```

> GetConnectionStatus returns the current connection status of the device.

```csharp
ConnectionStatus status = gripablePlay.GetConnectionStatus();
```

GetConnectionStatus returns the current connection status of the device. This can be either CONNECTING, CONNECTED, DISCONNECTING or DISCONECTED.

### Return Values

Returns | Description
------- | -----------
CONNECTING | The device is in the middle of a connection attempt
CONNECTED | The device is connected
DISCONNECTING | The device is in the middle of a disconnection attempt
DISCONNECTED | The device is disconnected or not initialised

## IsConnected()

```csharp
bool IsConnected();
```

> IsConnected returns true or false whether the device is connected or not.

```csharp
bool isConnected = gripablePlay.IsConnected();
```

IsConnected returns true or false whether the device is connected or not. This is equivalent of checking if the connection status is CONNECTED.

### Return Values

Returns | Description
------- | -----------
true | The device is connected
false | The device is connecting, disconnecting, disconected or not initialised

## IsDisconnected()

```csharp
bool IsDisconnected();
```

> IsDisconnected returns true or false whether the device is disconnected or not.

```csharp
bool isDisconnected = gripablePlay.IsDisconnected();
```

IsDisconnected returns true or false whether the device is disconnected or not. This is equivalent of checking if the connection status is DISCONNECTED.

### Return Values

Returns | Description
------- | -----------
true | The device is disconnected or not initialised
false | The device is connecting, connected or disconnecting

# Getting Grip Data

Grip Data contains the sensor information from squeezing the Gripable Play.

## SubscribeToGripData()

```csharp
bool SubscribeToGripData();
```

> SubscribeToGripData attempts to subscribe to the device grip data, returns true if successful.

```csharp
bool subscriptionSuccessful = gripablePlay.SubscribeToGripData();
```

SubscribeToGripData attempts to subscribe to the device grip data. It returns true if the subscription was successful.

### Return Values

Returns | Description
------- | -----------
true | The subscription was successful
false | The subscription failed

## UnsubscribeFromGripData()

```csharp
bool UnsubscribeFromGripData();
```

> UnsubscribeFromGripData attempts to unsubscribe from the device grip data, returns true if successful.

```csharp
bool unsubscriptionSuccessful = gripablePlay.UnsubscribeFromGripData();
```

UnsubscribeFromGripData attempts to unsubscribe from the device grip data. It returns true if the unsubscription was successful.

### Return Values

Returns | Description
------- | -----------
true | The unsubscription was successful
false | The unsubscription failed

## IsSubscribedToGripData()

```csharp
bool IsSubscribedToGripData();
```

> IsSubscribedToGripData returns true or fals whether the device is subscribed to grip data or not.

```csharp
bool isSubscribed = gripablePlay.IsSubscribedToGripData();
```

IsSubscribedToGripData returns true or fals whether the device is subscribed to grip data or not.

### Return Values

Returns | Description
------- | -----------
true | The device is subscribed to Grip Data
false | The device is not subscribed to Grip Data or not initialised

## GetGripData()

```csharp
GripData GetGripData();
```

> GetGripData returns an object containing data from the grip sensor.

```csharp
GripData gripData = gripablePlay.GetGripData();
```

GetGripData returns an object containing data from the grip sensor, including a the grip force and center of pressure.

### Return Values

Returns | Description
------- | -----------
gripData | Returns an object containg the grip data
empty GripData | Returns an empty GripData object if not subscribed or not initialised

## GetGripForce()

```csharp
float GetGripForce();
```

> GetGripForce returns a float value for Grip Force in Kg.

```csharp
float gripForce = gripablePlay.GetGripForce();
```

GetGripForce returns the force in Kg that is currently being applied to the Gripable. It is sampled at 50Hz.

### Return Values

Returns | Min | Max | Description
------- | --- | --- | -----------
gripForce | -129f | 128f | Returns the Grip Force in Kg as a float
0f | | | Returns 0 if the device is not subscribed or not initialised

# Getting Quaternion Data

Quaternion Data contains the sensor information about the orientation of the device in 3D space.

## SubscribeToQuaternionData()

```csharp
bool SubscribeToQuaternionData();
```

> SubscribeToQuaternionData attempts to subscribe to the device quaternion data, returns true if successful.

```csharp
bool subscriptionSuccessful = gripablePlay.SubscribeToQuaternionData();
```

SubscribeToQuaternionData attempts to subscribe to the device quaternion data. It returns true if the subscription was successful.

### Return Values

Returns | Description
------- | -----------
true | The subscription was successful
false | The subscription failed

## UnsubscribeFromQuaternionData()

```csharp
bool UnsubscribeFromQuaternionData();
```

> UnsubscribeFromQuaternionData attempts to unsubscribe from the device quaternion data, returns true if successful.

```csharp
bool unsubscriptionSuccessful = gripablePlay.UnsubscribeFromQuaternionData();
```

UnsubscribeFromQuaternionData attempts to unsubscribe from the device quaternion data. It returns true if the unsubscription was successful.

### Return Values

Returns | Description
------- | -----------
true | The unsubscription was successful
false | The unsubscription failed

## IsSubscribedToQuaternionData()

```csharp
bool IsSubscribedToQuaternionData();
```

> IsSubscribedToQuaternionData returns true or fals whether the device is subscribed to quaternion data or not.

```csharp
bool isSubscribed = gripablePlay.IsSubscribedToQuaternionData();
```

IsSubscribedToQuaternionData returns true or fals whether the device is subscribed to quaternion data or not.

### Return Values

Returns | Description
------- | -----------
true | The device is subscribed to Quaternion Data
false | The device is not subscribed to Quaternion Data or not initialised

## GetQuaternion()

```csharp
Quaternion GetQuaternion();
```

> GetQuaternion returns a Quaternion object representing the orientation of the device.

```csharp
Quaternion deviceQuaternion = gripablePlay.GetQuaternion();
```

GetQuaternion returns a Quaternion object representing the orientation of the device.

### Return Values

Returns | Description
------- | -----------
deviceQuaternion | The device orientation expressed through a Quaternion object
default Quaternion | Returns a default Quaternion (x,y,z=0, w=1) if the device is not subscribed or not initialised

## GetSensorFrameRpy()

```csharp
Vector3 GetSensorFrameRpy();
```

> GetSensorFrameRpy returns a Vector3 object representing the RPY of the device.

```csharp
Vector3 rollPitchYaw = gripablePlay.GetSensorFrameRpy();
```

GetSensorFrameRpy returns a Vector3 object representing the current Roll, Pitch and Yaw of the device.

### Return Values

Returns | Description
------- | -----------
rollPitchYaw | The device roll, pitch and yaw expressed through a Vector3 object
Vector3.zero | Returns a zeroed Vector3 if the device is not subscribed or not initialised

# Getting Motion Data

Motion Data contains the sensor information about the movements of the device, collected from the gyroscope, accelerometer and magnetometer.

## SubscribeToMotionData()

```csharp
bool SubscribeToMotionData();
```

> SubscribeToMotionData attempts to subscribe to the device motion data, returns true if successful.

```csharp
bool subscriptionSuccessful = gripablePlay.SubscribeToMotionData();
```

SubscribeToMotionData attempts to subscribe to the device motion data. It returns true if the subscription was successful.

### Return Values

Returns | Description
------- | -----------
true | The subscription was successful
false | The subscription failed

## UnsubscribeFromMotionData()

```csharp
bool UnsubscribeFromMotionData();
```

> UnsubscribeFromMotionData attempts to unsubscribe from the device motion data, returns true if successful.

```csharp
bool unsubscriptionSuccessful = gripablePlay.UnsubscribeFromMotionData();
```

UnsubscribeFromMotionData attempts to unsubscribe from the device motion data. It returns true if the unsubscription was successful.

### Return Values

Returns | Description
------- | -----------
true | The unsubscription was successful
false | The unsubscription failed

## IsSubscribedToMotionData()

```csharp
bool IsSubscribedToMotionData();
```

> IsSubscribedToMotionData returns true or fals whether the device is subscribed to motion data or not.

```csharp
bool isSubscribed = gripablePlay.IsSubscribedToMotionData();
```

IsSubscribedToMotionData returns true or fals whether the device is subscribed to motion data or not.

### Return Values

Returns | Description
------- | -----------
true | The device is subscribed to Motion Data
false | The device is not subscribed to Motion Data or not initialised

## GetLinearAcceleration()

```csharp
Vector3 GetLinearAcceleration();
```

> GetLinearAcceleration returns a Vector3 object representing the linear acceleration of the device.

```csharp
Vector3 linearAcceleration = gripablePlay.GetLinearAcceleration();
```

GetLinearAcceleration returns a Vector3 object representing the linear acceleration of the device alongside its axes, expressed in G.

### Return Values

Returns | Min (per axis) | Max (per axis) | Description
------- | -------------- | -------------- | -----------
linearAcceleration | -16f | 16f | A Vector3 representing the linear acceleration of the device, expressed in G
Vector3.zero | | | Returns a zeroed Vector3 if the device is not subscribed or not initialised

## GetAngularVelocity()

```csharp
Vector3 GetAngularVelocity();
```

> GetAngularVelocity returns a Vector3 object representing the angular velocity of the device.

```csharp
Vector3 angularVelocity = gripablePlay.GetAngularVelocity();
```

GetAngularVelocity returns a Vector3 object representing the angular velocity of the device alongside its axes, expressed in degrees/sec.

### Return Values

Returns | Min (per axis) | Max (per axis) | Description
------- | -------------- | -------------- | -----------
angularVelocity | -200f | 200f | A Vector3 representing the angular velocity of the device, expressed in degrees/sec
Vector3.zero | | | Returns a zeroed Vector3 if the device is not subscribed or not initialised

## GetMagneticHeading()

```csharp
Vector3 GetMagneticHeading();
```

> GetMagneticHeading returns a Vector3 object representing the magnetic heading of the device.

```csharp
Vector3 magneticHeading = gripablePlay.GetMagneticHeading();
```

GetMagneticHeading returns a Vector3 object representing the magnetic heading of the device alongside its axes, expressed in microteslas.

### Return Values

Returns | Min (per axis) | Max (per axis) | Description
------- | -------------- | -------------- | -----------
magneticHeading | -4912f | 4912f | A Vector3 representing the magnetic heading of the device, expressed in microteslas
Vector3.zero | | | Returns a zeroed Vector3 if the device is not subscribed or not initialised

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

# Using UdpClientManager

The UdpClientManager class can be very useful to sen serialised data of the connected Gripable Plays to a machine through UDP. The UdpClientManager can be accessed via the UdpManager static attribute of the Gripable plugin.

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
