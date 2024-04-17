# MConHudSdk
<div align="left">
  <img src="https://img.shields.io/badge/Kotlin-0095D5?style=flat&logo=kotlin&logoColor=white"/>
  <img src="https://img.shields.io/badge/Platform-Android-green.svg"/> 
  <img src="https://img.shields.io/badge/Kotlin-1.6.20-orange.svg"/>    
  <img src="https://img.shields.io/badge/Release-v1.0-blue.svg"/>  
  <img src="https://img.shields.io/badge/License-MIT-lightgray.svg"/>     
</div>

## Installation
MConHudSdk를 사용하시려면 아래 절차에 따라 설정하세요:

Step 1. 프로젝트의 루트 디렉토리에 `libs` 폴더를 생성합니다. 이미 `libs` 폴더가 있는 경우 이 단계를 건너뛸 수 있습니다.

Step 2. 프로젝트의 `build.gradle` 파일에 다음을 추가하세요:

```kotlin
repositories {
    flatDir {
        dirs 'libs'
    }
}
```

Step 3. dependencies에 AAR 라이브러리를 추가하세요:

```
dependencies {
    implementation(name: 'mconhudsdk-1.0-release', ext: 'aar')
}
```

## Auth
You can use the SDK after initializing it with the following code.
```kotlin
// notification is for executing [MConHudCore]. If null is passed, the default Notification will be posted.
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

MConHudSdk.shared().sendJunctionViewInfo(
  clearCodes= clearCodes       // Provide the clear codes to be cleared in the form of an array.
)
```
If multiple indicators need to be cleared simultaneously, provide the "clearCode" value in the form of an array.
```kotlin
val clearCodes: ArrayList<ClearCode> = arrayListOf(ClearCode.TURN_BY_TURN, ClearCode.SAFETY)

MConHudSdk.shared().sendJunctionViewInfo(
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

## Firmware Update
```kotlin
val fwFile= File(firmwareFilePath)
val filePath= fwFile.path
val deviceName= "MHUD"
val version= "212201"
val fileSize= 794192
val updateType= 0
val crc= "0x5699"

MConHudSdk.shared().startFirmwareUpdate(
  filePath= filePath,       // 펌웨어 파일 경로
  deviceName= deviceName,   // HUD의 블루투스 스캔 기기명
  version= version,         // 업데이트 할 펌웨어 버전
  fileSize= fileSize,       // 펌웨어 파일의 사이즈
  updateType= updateType,   // 펌웨어 업데이트 타입 (0: 일반 업데이트, 1: 강제 업데이트)
  crc= crc                  // 체크썸
)
```
[MConHudFirmwareDelegate]를 통해 현재 펌웨어 정보와 펌웨어 업데이트 진행 및 완료 상태를 받을 수 있습니다.
```kotlin
class MainActivity : AppCompatActivity(), MConHudDelegate {
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
    // 펌웨어 업데이트 진행률(%)
}

override fun firmwareUpdateComplete() {
    // 펌웨어 업데이트 완료
}

override fun firmwareUpdateError(error: MConHudSdkError) {
    // 펌웨어 업데이트 오류
}

override fun receiveFirmwareInfo(firmwareInfo: FirmwareInfo) {
    // modelName, firmwareVersion, firmwareServerLastVersion, needUpdate
}
```

## License
N/A. Update if needed.

