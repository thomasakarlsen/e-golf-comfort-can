# e-Golf Comfort Canbus

With the 2G network hutdowns coming the next couple of years I decided to take things into my own hands and reverse engineer how the OCU in my car works in hopes to replace it with something of my own.

In this repository I will attempt to gather all the knowledge I have learnt and share it so it can help other owners of e-Golfs.

All of this work is done on my MY2018 e-Golf. The e-Golf is based on the MQB platform and so the information in this repository may be useful for other MQB cars.

## Why the comfort canbus and not the OBD port?

On MQB platform cars the OBD port is not directly connected to any of the canbusses but rather to the gateway module. The gateway module only forwards messages from the OBD port to the other canbusses when the ignition is on. Because of this limitation it would be impossible to implement all the remote control features by only connecting to the OBD port.

Additionally, by looking at the wiring schematics the orignial OCU is only connected to the comfort canbus so implementing all the features of it should be possible by only connecting to the comfort canbus.

## Examples

In the examples folder you can find trace files that contain a set of CAN Frames you can send on the e-Golfs comfort can to perform an action.

TODO: Cleanup examples, some messages (like updating the global BCP profile) are not required

**Start climate**: Wakes the car up from sleep and starts climatisation with the settings that are stored in the car

**Stop climate**: Stops climatisation

## DONE

- [Connecting and reading messages from the comfort canbus](pages/the-comfort-canbus.md)
- [Understanding how the OCU talks to other modules](pages/how-modules-talk.md)
- [Understanding the Battery Control/Manager and Battery Control Profiles](pages/the-battery-manager.md) (WIP)
- Controlling climatisation (start, stop and settings)

## TODO

- Charging controls (start and stop)
- Door controls (lock and unlock)
- Read data/car state/status (SoC, speed, lights, etc.) (Anything thats just gathering data not actual commands to control)



## Mentions

Karl Dietmann - Writeup on the [BAP protocol](https://blog.dietmann.org/?p=324).

Andrew Leech - Setup for decompiling MIB 2 software. [mib2-lsd-patching](https://gitlab.com/alelec/mib2-lsd-patching).
