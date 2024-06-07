# The comfort canbus

In MQB vehicles the comfort canbus is what connects all the systems and modules which are not critical for the actual driving of the car but rather provide nice features for the occupants of the car. Among the modules on the comfort canbus is the KESSY key system, heater controls, steering column, doors, enterainment system and OCU.

The comfort canbus consists of two wires in a twisted pair:

CAN-HIGH: Orange with green stripes  
CAN-LOW: Orange with brown stripes

> [!TIP]
> While the color specification is orange, I have found that the cable does look a bit more yellow-ish than orange. Your milage may vary.

The canbus runs at `500 Kbps` speed and contains extended messages (messages with 29-bit addresses).  
It does **NOT** use CAN FD, FlexRay, etc...

## Physically connecting to the comfort canbus

> [!NOTE]
> This is just how I did it, you can connect to the comfort canbus at any point in the car you like.

Because my end goal is to replace the OCU with something of my own, I went straight for the OCU connector.  
The OCU is located behind the speedometer, so in order to access the OCU you need to remove the speedometer.

Removing the speedometer is fairly simple. You need to remove some of the trim around it, unscrew two screws in the bottom and pull it out. If its the first time the speedometer is being removed it will most likely be quite hard to pull out as the plastic clips holding it in are quite chunky and stiff.

Once the speedometer is out the OCU can be found right behind it, and on its connector you can find the comfort canbus wires.

I will try to remember to take pictures the next time I remove the speedometer and update this part with a better step-by-step guide.

## The gateway

Connected to more or less all of the canbusses is the gateway. The gateways role is to pass messages between the different canbusses (as some of them run at different speeds). It also serves as the entry point for diagnostics tools, but it only allows this if the cars ignition is on.

The gatway is the reason we have data like the cars speed, high-voltage battery status, etc. available on the comfort canbus as these messages are normally sent on different canbusses running at a higher speed.
