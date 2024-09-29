# The battery control unit / manager

The battery control unit is a logical device (LSG) available over a BAP channel. I am unsure what physical module (FSG) is actually providing this logical device. I suspect it could be the body control module (BCM) or the high-voltage battery module.

From what I can tell the battery control unit is what handles all day-to-day high-voltage needs and usages. By this I mean it contains stats about the battery, but also manages features like climatisation, charging, etc.

## Battery Control Profiles

Battery control profiles are profiles stored in the BCU that define settings regarding climatisation and charging the high-voltage battery.
The BCPs is what the infotainment and volkswagen app commonly refer to as "Charge locations", the profiles for departure timers where you can define settings like:

- Upper charge percentage limit
- Should the car charge up for the departure
- Maximum amperage to charge with
- Should the car pre-heat for the departure

You can create or delete battery control profiles (or charge locations) as you please from the infotainment system and assign one of them to each of the available departure timers.

However, there is one special battery control profile that is not exposed to end-users as a charge location.
The car has a built-in BCP, at index 0, where it stores more global settings like:

- Maximum charge limit (when charging without a dep. timer)
- Minimum charge percentage (the car will always instantly charge up to this when using a dep. timer, then wait for the remaining)
- Should the battery be used during climatisation
- Target climatisation temperature

There are also more settings that are not exposed to end users at all, however it varies what settings are actually supported and used by the car:

- Seat heating when running climatisation (?)
- Minimum range (?)
- Target charge duration (?)
- Lead time (?)
- Hold time plug (?)
- Hold time battery (?)

One of the hold time options do control how long the car runs climatisation for, allowing us to increase or decrease this.

TODO: Examples, and explain which hold time is used to change climatisation time.

## BAP messages

Explain and provide examples of how to read the BCP array and how to modify a BCP.

## Controlling climatisation using the high-voltage battery

### Waking up the vehicle

& initialising the BAP channel

### Changing climatisation settings

### Starting climatisation

### Stopping climatisation
