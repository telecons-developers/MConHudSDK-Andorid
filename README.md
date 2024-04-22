# MConHudSdk
<div align="left">
  <img src="https://img.shields.io/badge/Kotlin-0095D5?style=flat&logo=kotlin&logoColor=white"/>
  <img src="https://img.shields.io/badge/Platform-Android-green.svg"/> 
  <img src="https://img.shields.io/badge/Kotlin-1.6.20-orange.svg"/>    
  <img src="https://img.shields.io/badge/Release-v1.0-blue.svg"/>  
</div>

## Installation
Follow the below steps for using MConHudSdk to set:

Step 1. Create “libs” folder on root directory of the project. If “libs” folder is existed then may skip this step.

Step 2. Add the below at build.gradle file of the project:

```kotlin
repositories {
    flatDir {
        dirs 'libs'
    }
}
```

Step 3. Add AAR library at dependencies:

```
dependencies {
    implementation(name: 'mconhudsdk-1.0-release', ext: 'aar')
}
```

## Auth
You can use the SDK after initializing it with the following code.
```kotlin
// To initialize completely, the workable network is needed.
MConHudSdk.shared().initialize(application= application, appkey= "appkey") { error ->
    if(error == null) {
       // authorization success
    } else {
       // authorization fail
    }
}
```

## Scan Device
The following code allows you to scan your HUD device via Bluetooth.
```kotlin
class MainActivity : AppCompatActivity(), MConHudScanDelegate {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        ...
        // Set Scan delegate
        MConHudSdk.shared().hudScanDelegate = this

        // The scanner will automatically stops according to the timeout set by the system or when trying to connect HUD device
        MConHudSdk.shared().startScanPeripheral()
        ...
    }
}
```
You will receive a list of scanned HUD devices, scan completion, and scan errors through [MConHudScanDelegate]
```kotlin
//
// MConHudScanDelegate
//
override fun scanPeripherals(peripherals: MutableList<MConHudPeripheral>) {
    // List sorted by rssi sensitivity of scanned HUD devices
}
override fun scanTimeOut() {
    // When scan finished
}
override fun error(error: MConHudSdkError) {
    // An error occurred
}
```

## Bluetooth Connect Device
You can try connecting the HUD with the following code.
```kotlin
// peripheral is MConHudPeripheral
MConHudSdk.shared().connectPeripheral(peripheral= MConHudPeripheral)
```
You will receive a signal via [MConHudScanDelegate] when a connection is established or a connection error occurs.
```kotlin
//
// MConHudScanDelegate
//
override fun connectPeripheralResult(peripheral: peripheral) {
    // Connect success
}
override fun error(error: MConHudSdkError) {
    // Connect failed or an error occurred
}
```

## Bluetooth Disconnect Device
Bluetooth connection will be disconnected once turn off HUD device's power or call the following code.
```kotlin
// peripheral is MConHudPeripheral
MConHudSdk.shared().disconnectPeripheral(peripheral= peripheral)
```
You are to receive a disconnection signal via [MConHudScanDelegate] when the connection is terminated.
```kotlin
//
// MConHudScanDelegate
//
override fun disconnectedPeripheral() {
    // lost connection with HUD device
}
```

## Turn by turn Message
```kotlin
val turnByTurnCode: TurnByTurnCode = TurnByTurnCode.STRAIGHT
// distance is in meters
val distance = 200
MConHudSdk.shared().sendTurnByTurnInfo(tbtCode= turnByTurnCode, distance= distance)
```

## Safety Message
```kotlin
val safetyCodeList: ArrayList<SafetyCode> = arrayListOf(SafetyCode.TRAFFIC_MONITORING_AREA)
val remainDistance = 150

MConHudSdk.shared().sendSafetyInfo(
  safetyCodes= safetyCodeList,       // Provide the safety codes which is to be flashed in the form of an array.  
  remainDistance= remainDistance     // Remaining distance (m)
)
```
If the multiple safety indicators are to be flashed at the same time, provide the value for safetyCode in the form of an array.
```kotlin
val safetyCodes: ArrayList<SafetyCode> = arrayListOf(SafetyCode.TRAFFIC_MONITORING_AREA, SafetyCode.ACCIDENT_PRONE_ZONES)
val remainDistance = 150

MConHudSdk.shared().sendSafetyInfo(
  safetyCodes= safetyCodes,          // Provide the safety codes which is to be flashed in the form of an array.  
  remainDistance= remainDistance     // Remaining distance (m)
)
```

## Speeding Message
```kotlin
val limitSpeed = 30
val isSpeeding = false

MConHudSdk.shared().sendSpeedingInfo(
  limitSpeed= limitSpeed,            // If there's a speed limit in the specific section, provide the speed limit.
  isSpeeding= false                  // If the current vehicle is speeding, pass 'true'. When HUD get 'true', it activates the speeding alert buzzer.
)
```

## Junction View Message
```kotlin
val junctionViewCodeList: ArrayList<JunctionViewCode> = arrayListOf(JunctionViewCode.SERVICE_LANE)

MConHudSdk.shared().sendJunctionViewInfo(
  junctionViewCodes= junctionViewCodeList       // Provide the junction view codes which is to be flashed in the form of an array.  
)
```
If the multiple junction view indicators are to be flashed at the same time, provide the value for junctionViewCode in the form of an array.
```kotlin
val junctionViewCodes: ArrayList<JunctionViewCode> = arrayListOf(JunctionViewCode.SERVICE_LANE, JunctionViewCode.UNDERPASS)

MConHudSdk.shared().sendJunctionViewInfo(
  junctionViewCodes= junctionViewCodeList       // Provide the junction view codes which is to be flashed in the form of an array.  
)
```

## Car Speed Message
```kotlin
val speed = 100
MConHudSdk.shared().sendCarSpeed(speed= speed)
```

## Fuel Message
```kotlin
val fuelCode: FuelCode = FuelCode.FUEL_PUMP
MConHudSdk.shared().sendFuel(fuelCode= fuelCode)
```

## Clear Message
```kotlin
val clearCodes: ArrayList<ClearCode> = arrayListOf(ClearCode.TURN_BY_TURN)

MConHudSdk.shared().sendClear(
  clearCodes= clearCodes       // Provide the clear codes to be cleared in the form of an array.
)
```
If multiple indicators need to be cleared simultaneously, provide the "clearCode" value in the form of an array.
```kotlin
val clearCodes: ArrayList<ClearCode> = arrayListOf(ClearCode.TURN_BY_TURN, ClearCode.SAFETY)

MConHudSdk.shared().sendClear(
  clearCodes= clearCodes       // Provide the clear codes to be cleared in the form of an array.
)
```

## HUD Time
Transmitting the current time as a String, utilizing the date and time format yyyyMMddHHmmss.
```kotlin
val dateTime = SimpleDateFormat("yyyyMMddHHmmss", Locale.getDefault()).format(Date())

MConHudSdk.shared().sendTime(yyyyMMddHHmmss= dateTime)
```
If the HUD requires time updates, events are delivered through [MConHudDelegate].
```kotlin
class MainActivity : AppCompatActivity(), MConHudDelegate {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        ...
        // Set delegate
        MConHudSdk.shared().hudDelegate = this
        ...
    }
}

//
// MConHudDelegate
//
override fun receiveTimeUpdate() {
    // sendTime
}
```

## HUD Brightness
Change the brightness of HUD.
```kotlin
// LOW, MEDIUM, HIGH
val brightnessLevel: BrightnessLevel = BrightnessLevel.LOW
MConHudSdk.shared().sendHudBrightnessLevel(brightnessLevel= brightnessLevel)
```
You can receive the current brightness settings of the HUD through [MConHudDelegate].
```kotlin
class MainActivity : AppCompatActivity(), MConHudDelegate {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        ...
        // Set delegate
        MConHudSdk.shared().hudDelegate = this
        MConHudSdk.shared().getHudBrightnessLevel()
        ...
    }
}

//
// MConHudDelegate
//
override fun receiveHudBrightnessLevel(brightnessLevel: BrightnessLevel) {
    // LOW, MEDIUM, HIGH
}
```

## HUD Buzzer
Change the beep sound volume of HUD.
```kotlin
// MUTE, LOW, MEDIUM, HIGH
val buzzerLevel: BuzzerLevel = BuzzerLevel.LOW
MConHudSdk.shared().sendHudBuzzerLevel(buzzerLevel= buzzerLevel)
```
You can receive the current buzzer status of the HUD through [MConHudDelegate].
```kotlin
class MainActivity : AppCompatActivity(), MConHudDelegate {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        ...
        // Set delegate
        MConHudSdk.shared().hudDelegate = this
        MConHudSdk.shared().getHudBuzzerLevel()
        ...
    }
}

//
// MConHudDelegate
//
override fun receiveHudBuzzerLevel(buzzerLevel: BuzzerLevel) {
    // MUTE, LOW, MEDIUM, HIGH
}
```

## Firmware Infomation
Able to get the current information of firmware through [MConHudFirmwareDelegate].
```kotlin
class MainActivity : AppCompatActivity(), MConHudFirmwareDelegate {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        ...
        // Set delegate
        MConHudSdk.shared().hudFirmwareDelegate = this
        MConHudSdk.shared().getFirmwareInfo()
        ...
    }
}

//
// MConHudFirmwareDelegate
//
override fun receiveFirmwareInfo(firmwareInfo: FirmwareInfo) {
    // modelName, firmwareVersion
}
```

## Firmware Update
Need to update the firmware at the latest version, if its version is not up-to-date.
```kotlin
val fwFile= File(firmwareFilePath)
val filePath= fwFile.path
val deviceName= "MHUD"
val version= "212201"
val fileSize= 794192
val crc= "0x5699"

MConHudSdk.shared().startFirmwareUpdate(
  filePath= filePath,       //  Firmware file path
  deviceName= deviceName,   // Scanned device name by BT for HUD
  version= version,         // Firmware version to be updated
  fileSize= fileSize,       // Firmware file size to be updated
  crc= crc                  // checksum
)
```
[MConHudFirmwareDelegate] is to indicate the progress and complete status of the firmware update

```kotlin
class MainActivity : AppCompatActivity(), MConHudFirmwareDelegate {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        ...
        // Set delegate
        MConHudSdk.shared().hudFirmwareDelegate = this
        MConHudSdk.shared().getFirmwareInfo()
        ...
    }
}

//
// MConHudFirmwareDelegate
//
override fun firmwareUpdate(progress: Int) {
    // Update progress rate of FW
}

override fun firmwareUpdateComplete() {
    // FW update is completed
}

override fun firmwareUpdateError(error: MConHudSdkError) {
    // FW update got an error
}
```
