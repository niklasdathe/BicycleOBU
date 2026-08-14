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
| Main OBU | ESP32-S3 hub and ESP32-C5 ITS-G5 architecture under development |
| System integration | Physical end-to-end validation pending |

Do not treat source review or simulation as complete-system validation. Each
subproject records its own implemented and pending verification evidence.

## Architecture

```text
Phone OBU
   | Bluetooth Low Energy
ESP32-S3 embedded hub
   |-- ESP32-C5 ITS-G5 radio
   |-- CAN-connected modules
   `-- Local or BLE sensors
```

The phone performs visualization, navigation, application-level C-ITS
interpretation and ride recording. The ESP32-S3 provides the embedded data and
control boundary. The ESP32-C5 is reserved for ITS-G5 radio processing.

## Subprojects

| Path | Repository | Scope |
|---|---|---|
| [`phone-app`](phone-app/) | [PhoneOBU](https://github.com/niklasdathe/PhoneOBU) | Flutter rider interface, BLE client, navigation, C-ITS processing and ride data |
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
- Wired bicycle modules are expected to use a shared 5 V supply and a
  fault-tolerant vehicle bus; the final bus and connector system remain open.
- Mechanical assemblies must withstand bicycle vibration and environmental
  exposure; no enclosure or ingress-protection claim has been validated.
- Raw acquisition time, arrival time and data provenance must remain distinct
  across transport and storage boundaries.

## Planned components

| Component | Intended role | State |
|---|---|---|
| Main OBU | BLE gateway, vehicle bus, power and sensor coordination | Architecture defined; implementation pending |
| ITS-G5 radio | Receive and transmit cooperative transport messages | Architecture defined; implementation pending |
| IMU modules | Distributed bicycle motion measurements | Investigation |
| Smart lighting | Vehicle-bus-controlled head and tail lighting | Investigation |
| Energy system | Dynamo input, battery charging and regulated system power | Investigation |

## License

No project license has been selected. Until one is added, this repository and
its subprojects must not be presented as granting hardware, software or
documentation reuse rights.
