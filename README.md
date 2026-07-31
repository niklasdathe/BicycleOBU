
# iPhoneOBU
For now this repository is just a collection of ideas/requirements I have for an IOS based OBU for bicycles.

## Components of complete System
A list for components that would be cool in the complete system
### iPhone 
#### Software
- The app will be the ui, and brains of the OBU
- It communicates with ESP32S3 on mainboard through BLE
- Data collection and logging with export capabilities. Maybe upload to a server.
- Upload V2x messages to OpenTrafficMap
  
#### GUI
- Shows a optimal speed range to reach next green traffic light if it recieved valid data
- Warns if it detects an incoming collision
- Info if it thinks tire pressure is low
- Warning if its about to rain
- Integration with apple maps or google maps if possible
- Show surrounding vehicles like on an head up display

### Mainboard / OBU
#### Mechanical Mounting
- Must be rigid
- Attaches centrally to any bicycle handle bar
- The iPhone must mount rigidly as it provides the GUI
- Waterproof
#### Electronics
- Co-processor architecture or seperate boards for easier development and reusability
- Must access CAN Bus
- Must control battery charging and monitor + publish battery charge
#### Software
- ESP32C5
  - Will be configured as a V2x radio that listens to incoming messages and routes them to the esp32s3 via SPI/I2C or puts them on the CAN-Bus
  - Can transmit messages like alert messages with current location to other road units
- ESP32S3
  - Acts as the link between the iPhone and the onboard sensors/actors and communicates through BLE


### IMU units
#### Mechanical mounting 
- Mounting should be simple and mountable to different bicycles
- Mounting must be rigid and must not move
- Waterproof
#### Electronics
- ESP32 for discovery communication and wireless communication
#### Communication
- Either BLE for easy positioning anywhere on bike or CAN
#### Power
- Either battery based for short term installation or on 5V system power bus for long term installation

  
### Smart tail-/ headlights
#### Mechanical mounting 
- Should mount to standard headlight/taillight mounting spots
- Waterproof
#### Electronics
- ESP32 for discovery communication and wireless communication
#### Communication
- CAN, as a 5VDC power wire must be routed anyway
#### Power
- On 5V system power bus for long term installation
- Probably not feasible but might be worth investigating if power can be harvested from the spinning spoke magnet with a coil


### Wheel speed sensors
The implemented optical wheel-speed subsystem is maintained as the
[`IRBicycleWheelSpeedSensor`](https://github.com/niklasdathe/IRBicycleWheelSpeedSensor)
submodule. It contains the KiCad hardware, linked Python/ngspice simulation,
ESP-IDF firmware, verification artifacts, JLCPCB production files, and an
[interactive BOM](https://niklasdathe.github.io/IRBicycleWheelSpeedSensor/).

#### Mechanical mounting 
- Mounting should be simple and mountable to different bicycles
- Hall sensor on fork with magnet on spokes
- Waterproof
#### Electronics
- ESP32 for discovery communication and wireless communication
#### Communication
- Either BLE for easy positioning anywhere on bike or CAN
#### Power
- Either battery based for short term installation or on 5V system power bus for long term installation
- Probably not feasible but might be worth investigating if power can be harvested from the spinning spoke magnet with a coil


## Hardware subprojects

| Path | Purpose |
| --- | --- |
| `sensors/IRBicycleWheelSpeedSensor` | Configurable 940 nm IR spoke sensor with optional CAN integration |

Clone this repository together with all hardware subprojects:

```bash
git clone --recurse-submodules https://github.com/niklasdathe/BicycleOBU.git
```

Initialize them in an existing checkout:

```bash
git submodule update --init --recursive
```

  
### Metabo CAS battery
#### Mechanical mounting
- The battery should be mounted centrally on the frame
- Must be mechanically locked to be save against vibration
- Waterproof
#### Charging circuitry
- Charging with the onboard dynamo (6VAC @ 3W)
- Requires:
  -  variable 6VAC to 21VDC converter circuitry
  -  Battery charging circuitry
  -  Battery communication logic https://github.com/LukasGossmann/MetaboCAS
#### Power supply
- Power output needs to be converted from 18V down to 5V
- Power consumption of complete onboard system is constrained by average charging energy achieved by this system

  
## Idea/Question collection
- If BLE bandwidth isn't enough for high bandwidth sensor communication or unreliable, IEEE 802.15.4 TSCH could be investigated
- Could be fun to investigate if an IMU above the wheel can be used to estimate the tire pressure by looking at how dampened vertical oscillations are when driving over bumps
- If we have location and V2x messages from infrastructure, we can give recommendations on travelling speed to reach green light
- V2x data can be uploaded to OpenTrafficMap through roaming of iPhone
- Sensors should be easily discovered, connected and configured. Probably best to build a xiao based modular sensor component that can be configured to be an imu, a wheel speed sensor or something else.
- CAN controlled bicyle tire lock like on bike sharing bikes that can unlock via bus
- What can i learn from the data:
  - Can i reduce localization dependency of gnss or improve standstill acurracy with an ekf and the onboard sensors
  - Can bicycle turning radius be estimated by IMU data?
  - Can an IMU above the wheel  be used to estimate the tire pressure by looking at how dampened vertical oscillations are when driving over bumps
  - Can a map be created with how bumpy stretches of road are compared to others to create a heatmap (maybe useful for infrastructure improvements)
- Can roaming cost be reduced with LoRaWAN
- Can the phone camera be used for object or road unit detection
- What can legally be transmitted that would benefit the vru/cyclist
- Are people willing to pay for a system like this/ willing to install it
- Is CAN Bus the best candidate for a wire bicycle  bus. Is there a better protocoll with power over bus. Be existing cables can be reused.
- How can a battery be charged while driving so that is doesnt have to be recharged manually.
- How many sensors are needed to solve the kinematic model of a bicycle?
- What are the different kinematic models of a bicycle.
- Can localization be done completely without gnss?
- What can be done form the infrastructure providers to improve safety of vrus using obus
- What needs to be done in rural areas thats possible in citys

