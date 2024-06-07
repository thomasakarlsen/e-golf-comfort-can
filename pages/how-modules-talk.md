# How modules on the comfort canbus talk

For the purposes of this documentation there are two main types of communication on the canbus we will be looking at:

- Data channels (one-way communication)
- BAP Communication (two-way communication)

## Data channels

Data channels is a method of one-way communication on the canbus. Modules use data channels to publish data onto the canbus which other modules can then listen to and receive. There is no limit to how many data channels a module may publish and modules do not "share" channels. Only one module publishes data on a channel.

TODO: Provide examples of data channel messages.

## BAP Communication

The BAP protocol is a protocol Volkswagen implemented in CAN to allow two-way communication between modules on the canbus.

In BAP communications there are two types of devices:

- FSG: Functional control units (example: climate control panel)
- ASG: Display control unit (example: entertainment system)

A BAP channel usually consists of one FSG, but can consist of multiple ASGs. It is not possible for the different ASGs to communicate with eachother on the same BAP channel.

BAP channels use multiple CAN-IDs on the canbus. These are pre-determined CAN-IDs where only one device sends messages on. Normally there is one CAN-ID for FSG -> ASG communication and one CAN-ID for each ASG to allow for ASG -> FSG communication. This means if you have 3 ASGs communicating with one FSG there will be a total of 4 CAN-IDs in use. 3 for each of the ASGs and 1 for the FSG to talk back to the ASGs.

TODO: Provide examples of BAP channels & CAN-IDs.

### BAP Messages

There are two main types of BAP messages. Short and long ones.  
Short messages fit within one CAN message (8 bytes), while long messages span across multiple CAN frames.

Each message contains a BAP Header which contains information about about the operation, logical device ids and the function class the sending device wants to "perform an action" with on the receiving device.

Following the header is the data.

TODO: Add example image of a BAP short message.

Long BAP messages contain some extra data at the start of each CAN frame. The first frame of a BAP long message contains a preamble with information on the index of the message (devices can send multiple long messages at the same time), and the total data size of the message.  
The following frames of the BAP long message only contain the index of the message.

TODO: Add example image of a BAP long message.

#### Operations, logical device ids and function classes

> [!NOTE]
> This part is a rough explanation and needs to be rewritten.

Each device in the BAP system have atleast one logical device id, but it is possible for a device to have multiple. The logical device id is usually derived from the CAN ID of used for communication so you usually only see communication for one logical device on each CAN channel.  
The logical device id allows the receiving control unit to understand what part of it functionality the message is being sent to. It kind of acts as its own sub-control unit of the main unit.

Each logical device has a set of function ids. Some logical devices have unique functions only usable to them, but for the most part a lot of the function ids are standarized.

Each function id has its own set of valid operation codes. An operation code specifices what operation to perform on the given logical device and function id combination.

TODO: Add examples of different ops, lsgs and fcts

### Initializing a BAP channel

> [!NOTE]
> This is an observed behaviour from all ASGs on the bus. I have not tested if this is an actual requirement to communicate with the FSG.

When the car wakes up from sleep, or an ASG wants to initiate a channel to an FSG the ASG sends a command to get the BAP configuration from the FSG.  
The FSG then responds with a short message containing the BAP version in use as well as some information about the logical device provided on this channel.

TODO: Add example of initialization talk

### Further reading

[BAP reverse engineering (volkswagen PQ)](https://blog.dietmann.org/?p=324) by Karl Dietmann
