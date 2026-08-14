# Bicycle OBU

Bicycle-mounted research platform for cooperative transport, sensing and ride
data acquisition. The system separates the rider interface, embedded hub,
ITS-G5 radio and external sensors into independently versioned subprojects.

This is a research prototype. Experimental safety functions are not certified
and do not replace rider attention.

## Status

| Area | Current evidence |
|---|---|
| Phone application | Flutter Android/iOS application, BLE client, C-ITS processing, navigation and recording implemented |
| Wheel speed | IR through-beam sensor hardware, simulation and ESP-IDF firmware maintained as a submodule |
| Main OBU | Modular ESP-IDF ESP32-S3 hub + ESP32-C5 ITS-G5 firmware, local OLED/buzzer, GNSS/time and diagnostic logging maintained as a submodule |
| System integration | Physical end-to-end validation pending |

Do not treat source review, CI compilation or simulation as complete-system
validation. Each subproject records its own implemented and pending verification
evidence.

## Architecture

```mermaid
flowchart LR
    AIR[ITS-G5 / IEEE 802.11p] <--> C5[ESP32-C5\nradio endpoint]
    C5 <--> |Versioned CRC SPI| S3[ESP32-S3\nembedded hub]

    GNSS[L76K GNSS + PPS] --> S3
    RTC[RTC holdover] <--> S3
    CAN[CAN bicycle modules] -.-> S3
    WS[Wheel-speed / BLE sensors] -.-> S3
    PHONE[PhoneOBU\nFlutter rider interface] <-. BLE .-> S3

    S3 --> HMI[Local HMI model] --> DISPLAY[Prototype display]
    S3 --> WARN[Warning controller] --> BUZZER[Local audible warning]
    S3 --> LOG[Diagnostic logger] --> SD[microSD]

    PHONE --> NAV[Navigation / maps]
    PHONE --> RIDE[Scientific ride recording]
    PHONE --> OTM[OpenTrafficMap / network services]
    S3 -. optional direct uplink .-> OTM
```

The phone performs full C-ITS decoding, visualization, navigation, advanced
algorithms and canonical scientific ride recording. The ESP32-S3 is the embedded
hub and retains the minimum local functionality required for acquisition,
phone-less fallback warnings/HMI and standards-oriented outbound VAM generation.
The ESP32-C5 is reserved for ITS-G5 radio RX/TX. Local audible warnings are an
independent warning-output path rather than a function of phone or display
availability.

## Subprojects

| Path | Repository | Scope |
|---|---|---|
| [`phone-app`](phone-app/) | [PhoneOBU](https://github.com/niklasdathe/PhoneOBU) | Flutter rider interface, BLE client, navigation, C-ITS processing and ride data |
| [`mainboard-protoype`](mainboard-protoype/) | [MainboardOBU-prototype](https://github.com/niklasdathe/MainboardOBU-prototype) | ESP32-S3 hub, ESP32-C5 ITS-G5 endpoint, GNSS/time, local HMI/warnings and diagnostic logging |
| [`sensors/IRBicycleWheelSpeedSensor`](sensors/IRBicycleWheelSpeedSensor/) | [IRBicycleWheelSpeedSensor](https://github.com/niklasdathe/IRBicycleWheelSpeedSensor) | 940 nm spoke sensor, KiCad hardware, simulation and ESP-IDF firmware |

Clone the complete system:

```bash
git clone --recurse-submodules https://github.com/niklasdathe/BicycleOBU.git
```

Initialize subprojects in an existing checkout:

```bash
git submodule update --init --recursive
```

## System constraints

- The phone-to-hub interface uses Bluetooth Low Energy.
- The embedded architecture separates application/hub functions on the
  ESP32-S3 from ITS-G5 radio functions on the ESP32-C5.
- Loss of the phone must not stop embedded acquisition or configured local
  warning/HMI functions.
- Repeated receptions for one active DENM event must not create repeated rider
  notification episodes, while raw receptions remain available for logging and
  forwarding.
- Wired bicycle modules are expected to use the shared CAN architecture defined
  by the requirements; final carrier/connectors and electrical integration remain
  subject to hardware validation.
- Mechanical assemblies must withstand bicycle vibration and environmental
  exposure; no enclosure or ingress-protection claim has been validated.
- Raw acquisition time, arrival time and data provenance must remain distinct
  across transport and storage boundaries.

## Planned components

| Component | Intended role | State |
|---|---|---|
| Main OBU | Embedded hub, radio gateway, local warning/HMI, time and diagnostic storage | Prototype firmware implemented; HIL integration pending |
| ITS-G5 radio | Receive and transmit cooperative transport messages | C5 endpoint implemented; RF/ETSI conformance verification pending |
| IMU modules | Distributed bicycle motion measurements | Investigation |
| Smart lighting | Vehicle-bus-controlled head and tail lighting | Investigation |
| Energy system | Dynamo input, battery charging and regulated system power | Investigation |

## Source of truth

- The versioned requirements workbook defines system acceptance and architecture constraints.
- Each subproject README and `docs/` directory own implementation-specific evidence.
- Git submodule pins identify the exact revisions integrated into this system repository.
- Hardware-in-the-loop, RF, timing, warning and endurance tests remain necessary where the requirements prescribe physical verification.

## License

No project license has been selected. Until one is added, this repository and
its subprojects must not be presented as granting hardware, software or
documentation reuse rights.
