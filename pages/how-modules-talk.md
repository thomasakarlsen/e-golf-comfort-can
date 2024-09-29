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

Here is an example of how BAP short messages are built up:
![Encoding of a short BAP message](/images/bap_short-1200x515.png)
Image source: [BAP reverse engineering (volkswagen PQ)](https://blog.dietmann.org/?p=324) by Karl Dietmann

Short messages start with the BAP header, which is common for both short and long messages.
The header spans 2 bytes, containing 4 values within.

- Bit 0: Boolean to determine if the message is a short or long BAP message. 0 for short messages.
- Bit 1-3: Integer, defines the BAP operation code.
- Bit 4-9: Integer, defines the ID of the LSG (FSG or ASG) this message is for.
- Bit 10-15: Integer, defines the function ID of the LSG this message is for.

The remaining 6 bytes of the CAN message contains the payload. This is raw binary data which will be processed by the defined LSG depending on what operation code and function ID is set.

Long BAP messages contain some extra data at the start of each CAN frame. The first frame of a BAP long message contains a preamble with information on the index of the message (devices can send multiple long messages at the same time), and the total data size of the message.  
The following frames of the BAP long message only contain the index of the message.

![Encoding of a short BAP message](/images/bap_long-1200x794.png)
Image source: [BAP reverse engineering (volkswagen PQ)](https://blog.dietmann.org/?p=324) by Karl Dietmann

Long messages essentially work as a wrapper around a short message enabling the payload to be longer than the 6-byte limitation of short messages by spreading the payload across multiple CAN messages.
To help keep track of this long messages add some extra data at the start of each message to keep track of its state and message order.

We can split long messages up into two types of messages:

- Start message
- Continuation message

The start message contains a larger preamble to set up the long message by providing the expected payload length.
It also contains a message index. This message index is split into two parts, the first part defining the ID of the long message while the latter part defines the index of messages in the same long message the CAN frame belongs to.
Because long messages can have different IDs this allows for devices to send multiple long messages at a time, while the index allows the device to properly stitch the payload together once it has received the entire message.

The preamble spans the first 2 bytes of the CAN frame.

- Bit 0: Boolean to determine if the message is a short or long BAP message. 1 for long messages.
- Bit 1: Boolean to determine if the CAN frame is a start or continuation message. 0 for start, 1 for continuation.
- Bit 2-7: Integer, determines the message index
- Bit 8-15: Integer, determines the number of bytes the payload contains

After the preamble the same header section as found in short messages spans the next 2 bytes before it starts on the payload.
Because of the preamble the payload sent in the start message is limited to 4 bytes, while the rest of the data will be sent in continuation messages.

Continuation messages have a preamble similar to start messages, except it only spans the first byte of the CAN frame, omitting the byte used to define the expected payload length.

- Bit 0: Boolean to determine if the message is a short or long BAP message. 1 for long messages.
- Bit 1: Boolean to determine if the CAN frame is a start or continuation message. 0 for start, 1 for continuation.
- Bit 2-7: Integer, determines the message index

#### Operations, logical device ids and function classes

> [!NOTE]
> This part is a rough explanation and needs to be rewritten.

Each device in the BAP system have atleast one logical device id, but it is possible for a device to have multiple. The logical device id is usually derived from the CAN ID of used for communication so you usually only see communication for one logical device on each CAN channel.  
The logical device id allows the receiving control unit to understand what part of its functionality the message is being sent to. It kind of acts as its own sub-control unit of the main unit.

Each logical device has a set of function ids. Some logical devices have unique functions only usable to them, but for the most part a lot of the function ids are standarized.

Each function id has its own set of valid operation codes. An operation code specifices what operation to perform on the given logical device and function id combination.

TODO: Add examples of different ops, lsgs and fcts

### Initializing a BAP channel

> [!NOTE]
> This is an observed behaviour from all ASGs on the bus. I have not tested if this is an actual requirement to communicate with the FSG.

When the car wakes up from sleep, or an ASG wants to initiate a channel to an FSG the ASG sends a command to get the BAP configuration from the FSG.  
The FSG then responds with a short message containing the BAP version in use as well as some information about the logical device provided on this channel.

![Example of BAP channel initialization](/images/bap_config_handshake.png)
Image source: my own WIP BAP analyzer

The ASG sends a BAP message with the GET operation on the BAP Config function, there is no payload.
The FSG then responds with a RESET operation for the BAP Config, containing metadata about the BAP version and settings the FSG talks for this channel.

### Further reading

[BAP reverse engineering (volkswagen PQ)](https://blog.dietmann.org/?p=324) by Karl Dietmann
