# klingel_hass
PCB and code for a small board that intercepts the bell wiring and allows muting and remote door opening with home assistand.
## hardware
### PCB
The gerber and drill files can be found in the `kicad` folder.
### bill of materials
The following parts are used to assemble the PCB:
|reference|part name|value|package|
|---|---|---|---|
|C1-C5|capacitor|10 µF|smd 0805|
|D3,D4|bridge rectifier|ABS10|smd|
|D5,D11|red led||smd 0805|
|D1,D2,D6|diode|1N4007|tht DO-41|
|D7-D10|ws2812 led||smd|
|J1,J3|screw terminal|3 pins|tht 5.08 mm spacing|
|J2,J4,J5,J6|screw terminal|2 pins|tht 5.08 mm spacing|
|K1-K3|relay|5V SPDT|tht|
|Q1-Q3|transistor|BC547|tht TO-92|
|R2,R7,R8,R9,R12|resistor|1K|smd 0805|
|R1,R3,R4|resistor|10K|smd 0805|
|R5,R6|resistor|5|smd 0805|
|U1,U2|voltage regulator|AMS1117-1.5|smd SOT-223-3|
|U3|WEMOS D1 mini||tht|
|U4,U5|optocoupler|PC817|tht|

### connections
If you want to use the muting feature, you will have to cut the wires to your bell.
The voltage of your bell should be between 4V and 16V AC or DC.
A door opener could be connected to one of the relays.
|terminal name|description|
|----|----|
|5V|your 5V power supply (min. 1A)|
|bell-in|wires that normally supply power to your bell|
|bell-out|wires going to your bell|
|sense|additional input to detect voltage (4-16V AC or DC)|
|relayA,B|relay (NC = normally closes, NO = normally open)|

## software
I used esphome as a simple way to use the device with home assistant.

### configuration
Just change into the esphome directory, copy `secrets_example.yaml` to `secrets.yaml` and change its options accordingly.

### firmware upload
The firmware needs to be compiled with `esphome klingel_hass.yaml compile` and uploaded to a connected WEMOS D1 mini board with `esphome klingel_hass.yaml upload`.

### auto mute
A friend was worried about what would happen, when the device looses connection or gets stuck in a boot loop, while the bell was muted. So when auto mute is enabled, the bell stays connected/unmuted until a ring is detected and is only separating the bell connection, when the bell should be ringing. Depending on your bell, you might still hear a short ring until the relay opens.

## home assistant
The device should connect to wifi and be discovered by home assistant, which will ask you for the api password in `esphome/secrets.yaml` to connect.

### entities
|name|description|
|---|---|
|bell|changes to `on` when the bell is or should be ringing|
|sense|changes to `on` when a voltage is detected on the sense input|
|mute|mutes the bell when switched on|
|relayA|controls relay A|
|relayB|controled relay B|
|auto mute|controls auto mute feature|
|led|controls the small red led on the left|
|ws28|should control the ws2812 leds (untested)|
