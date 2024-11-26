# esphome-customs
Custom ESPHome control for various devices

## HZB-12B-B Ice Maker

This is a fairly popular ice maker, with a number of different design styles.  The code will likely work (along with your ESP8266) if the Ice Maker has the following characteristics:
- Power LED that flashes slowly when in Standby mode, and turns solid when on
- An Ice Full LED
- A Water Empty LED
- Separate LEDs for Small and Large ice

Note that we do *not* currently monitor the ice cube size. We don't make small ice and never press that button so we don't monitor it.

![HZB-12B-B Image](https://github.com/Kaldek/esphome-customs/raw/main/HZB-12B-B.jpg)

### Electrical connections
A circuit schematic will be created but in the interim we describe the connections.

#### ESP8266 power
You will need to provide a 3.3v regulator to power the ESP8266, tapping into the 13v power output from the transformer on the Ice Maker PCB.  A 7805 regulator exists on the ice maker PCB but it is 100mA only (78L05) and cannot power the ESP8266.

#### Power switch control
We use a 2n7000 MOSFET, with Source and Drain bridged across the pins shared by the power button on the power/LED daughterboard in the ice maker lid.  The gate of the MOSFET is driven by GPIO5.

#### Power state monitoring
We monitor the power feed to the Power LED, connected directly to GPIO12 of the ESP8266.  As this LED flashes when in Standby, we use ESPHome intervals and Lambda to determine if the ice maker is on or off (in standby).

#### Ice Full and Water Empty monitoring
We monitor the power feed to these LEDs, connected to GPIO4 (ice full) and GPIO16 (water empty).  When the power on these GPIO pins goes High, this is triggers these states.

#### Stuff that's less than optimal
I don't have a 10k pulldown resistor on the MOSFET gate, which I really should have installed.  If the ESP8266 ever goes offline, the ice machine goes bananas with the power switch constantly cycling.
