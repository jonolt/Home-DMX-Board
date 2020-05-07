# Home DMX - Hardware

is a simple arduino based DMX controller to controll led lights installed in your home to be used with DMX controllers and relais.

**Please Contact me if you wonna use the board, there may be unducumented bugs.**

The Firmware can be found in a extra git repository: [Home-DMX-Firmaware](https://github.com/jonolt/Home-DMX-Firmaware).

## Idea

The selection for the lighting scene in daily use should be as simple as possible. Therefore exactly two push buttons are used to move up and down in a list of lighting scenes. Since I am not a fan of idle transformers, a conventional light switch is used in my application to switch the whole system on and off. In the same way a corresponding scene could be set.

The individual scenes should be adjustable directly on the controller without additional hardware. The input of the scenes works like on lighting control desks without motor faders via potentiometer and buttons. To make the firmware easy (without programmer and co) and maintainable by everyone, an Arduino was used, because it has a USB converter and the IDE is easy to use and install.

The board has a DMX output and 4 PWM/IO Outputs as well as 2 inputs for button.

## Structure

The MAX481 (U2) is a transceiver for RS-485 and RS-422 communication based on DMX. It uses the TX and RX pins of the arduino. For a visual control of the DMX communication Leds are connected via buffer (U61).

Digital inputs and outputs a isolated via optocouplers (additional circuits are required to switch larger loads).

The display is controlled serially (SPI) via the library provided by the manufacturer.

The switches can be hardware or software or both debaunced. Theres also the option to connect them via diodes (D1-D4) to a interupt pin.

The board does not has to be fully equipt with the componets.

**External Power must be disconnected when programming the Ardino, when a DC/DC converter is used!**

### Hardware Logic (Isolated)

#### Button Input

A total of 4 buttons can be placed on the pcb. While SW2 and SW3 are hard wired SW1 and SW4 can be set up in 4 different ways to safe pins to use the hardware interupt:

1. **1 buttons 0 extra IO**: SW1 or SW2 are connected only to the Interrupt pin via Diode D1/D4 or a bridge at D1/D4. If an innterupt is triggered, and neither SW2 or SW3 pin is pulled low, SW1 or SW4 must have been pressed.
2. **2 buttons 1 extra IO**: as in one but with JP1/JP2 bridged and SW1/SW4 fully equiped.
3. **2 buttons 2 extra IO**: equip all parts and bridge JP1 and JP2. Interrupt can still be used for other things.
4. **1 buttons 1 extra IO**: as in 3. but with either SW1 or SW4

Of course the swictehs can be used withiout the interrupt by ommiting the diodes. In fact even the low pass filete can be ommited with proper software debouncing.

All Switches and the interupt line are pulled up by R1 to R4 and R9. R4, C1 ect. build low pass filter for hardware debouncing and can be ommited (with the resistors bridged).

Swicth 2 and 3 have an additional Isolated input for external switches operating on another voltage level or with different ground.

#### LCD-Display

The LCD-Display is controlled by 3 Wire SPI connected to the Hardwre SPI of the microcontroller. The MISO master input is not required for the display and is kept free in case additional SPI devices are added (for example via a shield). The Backlight is PWM dimmed (D11) via the transisor Q1.

#### Expansion Connector J1 J2

The Connector(s) has the identical pinout as the Arduino Micro. Also you could use a stackable female connector strip directly with the Arduino.

#### LEDs

In addition to a power LED (D6), there are various Status LEDs on the front side (pcb-back) showing the correct function of the PWM Outputs (D20-23) and the DMX Interface (D60, D61). The LED of the Arduino can be used as a system LED if D13 is not used otherwise.

### Hardware Interfaces (Unisoltated)

All outputs and inputs (connectors) are isolated by default to each other. Resitor Bridges bridges can be apllied (1XX, 2XX) to break those isolations, see chapter Hardware Bridging.

#### X30 - Button Input

Simple current limiting Resistor R30 and R31 for diode of the photocoupler. Needs to be selected for each application by the input voltage V_IN with the formular:

R=(V_IN-V_F)/I_F, V_F=1.2V I_F=20mA

#### X40 - PWM/Output

These autputs can be used in 2 ways. As digital output with pullups and as Low Side switch (max 50mA, V_CE=0.1-0.2V).

The voltage for the pullups can cpme from the connector, directly bridged from the input power (R140) or via the linear voltage regulator U140, R142 and R143 (kater ones bridging isolation).

The emmiters of the photocouplers can be accessed directly via J41-J43 and J40-2. Conection between those can be broken at the Jumpers JP40-JP42. This way all four outputs can be used iolsated to each other.

#### X50 - Power In

Power input conssits of two parts revers voltage protection and DCDC-Converter. The reverse voltage protection is a siple p channel circuit. For the DCDC-Converter are two footprints obove each other. One for a SIL7 package (with pins 1,2,4,6 connected) and one for a SIL8 package (with pin (1, 2, (3), 6, 7 connected) to allow for different converters to be installed.

#### X60 - DMX

DMX uses the TX/RX pins of the arduino. The MAX481 (U60) is a common RS-485 tranciver. See [www.mathertel.de/Arduino/DMXShield.aspx](http://www.mathertel.de/Arduino/DMXShield.aspx) for a detailed description.

### Hardware Bridging

Danger Zone. Only assemble these components if you know what you are doing as this will bridge isolations.

#### 1XX

Bridges between unisolated interfaces:

- **R130, R131**: VIN/GND to PWM/Ouitput
- **R140, R141**: VIN/GND to Button Input

#### 2XX

Bridges between unisolated interfaces and isolated internal circuit:

- **J250, J251**: Connect these Pads with a wire to use the buildin voltage regulator of the arduino (VIN).
- **R260-R264**: Bypass isolation of DMX interface.


## License and Copyright

Copyright (C) 2019, 2020 Johannes Nolte
 
This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, version 2.1 only.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see <https://www.gnu.org/licenses/>.
