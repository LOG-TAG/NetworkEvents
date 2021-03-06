NetworkEvents
===============================
[![Travis CI](https://travis-ci.org/pwittchen/NetworkEvents.svg?branch=master)](https://travis-ci.org/pwittchen/NetworkEvents)
[![Android Arsenal](https://img.shields.io/badge/Android%20Arsenal-NetworkEvents-brightgreen.svg?style=flat)](https://android-arsenal.com/details/1/1392) 
![Maven Central](https://img.shields.io/maven-central/v/com.github.pwittchen/networkevents.svg?style=flat)

Android library listening network events.

min sdk version = 9

Contents
--------
- [Overview](#overview)
- [Usage](#usage)
    - [Set permissions](#set-permissions)
    - [Initialize objects](#initialize-objects)
    - [Register and unregister objects](#register-and-unregister-objects)
    - [Subscribe for the events](#subscribe-for-the-events)
- [Examples](#examples)
- [Download](#download)
- [Tests](#tests)
- [License](#license)

Overview
--------

It is able to detect `ConnectivityStatus` when it changes.

```java
public enum ConnectivityStatus {
    UNKNOWN("unknown"),
    WIFI_CONNECTED("connected to WiFi"),
    WIFI_CONNECTED_HAS_INTERNET("connected to WiFi (Internet available)"),
    WIFI_CONNECTED_HAS_NO_INTERNET("connected to WiFi (Internet not available)"),
    MOBILE_CONNECTED("connected to mobile network"),
    OFFLINE("offline");
    ...
}    
```

In addition, it is able to detect situation when strength of the Wifi signal was changed with `WifiSignalStrengthChanged` event.

Usage
-----

### Set permissions

Add permissions to `AndroidManifest.xml` file inside the `<manifest>` tag.

```xml
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.CHANGE_WIFI_STATE" />
<uses-permission android:name="android.permission.CHANGE_NETWORK_STATE" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.INTERNET" />
```

### Initialize objects

In your activity add `Bus` field from [Otto Event Bus](http://square.github.io/otto/) library and `NetworkEvents` field.

```java
private Bus bus;
private NetworkEvents networkEvents;
```

Initialize objects in `onCreate(Bundle savedInstanceState)` method.

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    bus = new Bus();
    networkEvents = new NetworkEvents(this, bus);
}
```

#### NetworkEvents Customization

You can customize `NetworkEvents` object. E.g. you can set your own ping url and ping timeout:

```java
networkEvents = new NetworkEvents(this, bus)
        .withPingUrl("http://www.android.com")
        .withPingTimeout(50 * 1000);
```

You can also disable ping or Wifi Access Points Scan:

```java
networkEvents = new NetworkEvents(this, bus)
        .withoutPing()
        .withoutWifiAccessPointsScan();
```

If you disable Wifi Access Points Scan, `WifiSignalStrengthChanged` event will never occur.

If you disable ping, status `WIFI_CONNECTED_HAS_INTERNET` and `WIFI_CONNECTED_HAS_NO_INTERNET` won't be set.

### Register and unregister objects

Register `Bus` and `NetworkEvents` in `onResume()` method and unregister them in `onPause()` method.

```java
@Override
protected void onResume() {
    super.onResume();
    bus.register(this);
    networkEvents.register();
}

@Override
protected void onPause() {
    super.onPause();
    bus.unregister(this);
    networkEvents.unregister();
}
```

### Subscribe for the events

```java
@Subscribe
public void onConnectivityChanged(ConnectivityChanged event) {
    // get connectivity status from event.getConnectivityStatus()
    // and do whatever you want
}

@Subscribe
public void onWifiSignalStrengthChanged(WifiSignalStrengthChanged event) {
    // do whatever you want - e.g. read fresh list of access points
}
```

Examples
--------

* Look at `MainActivity` in application located in `example` directory to see how this library works.
* If you want to use this library with [Dagger](https://github.com/square/dagger), check `example-dagger` directory.
* Example with disabling ping and Wifi Access Points Scan is available in `example-disabling-ping-and-wifi-scan` directory.
* Example with customizing `pingUrl` and `pingTimeout` is available in `example-ping-customization` directory.

Download
--------

You can depend on the library through Maven:

```xml
<dependency>
    <groupId>com.github.pwittchen</groupId>
    <artifactId>networkevents</artifactId>
    <version>1.0.5</version>
</dependency>
```

or through Gradle:

```groovy
dependencies {
  compile 'com.github.pwittchen:networkevents:1.0.5'
}
```

Tests
-----

Tests are available in `network-events-library/src/androidTest/java/` directory and can be executed on emulator or Android device from Android Studio or CLI with the following command:

```
./gradlew connectedCheck
```

Test coverage report can be generated with the following command:

```
./gradlew createDebugCoverageReport
```

In order to generate report, emulator or Android device needs to be connected to the computer.
Report will be generated in the `network-events-library/build/outputs/reports/coverage/debug/` directory.

License
-------

    Copyright 2015 Piotr Wittchen

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.
