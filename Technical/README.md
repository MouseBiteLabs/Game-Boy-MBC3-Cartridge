# Technical Design Document

This write-up will serve as an attempt of explaining some of the features on this cartridge. I won't go into heavy detail about the functionality of the MBC3, but I will attempt to explain some higher level functions and the reasoning behind the circuit.

Please correct me on any information I may have gotten wrong.

## Schematic

![image](https://github.com/MouseBiteLabs/Game-Boy-MBC3-Cartridge/assets/97127539/a19b0cd8-b26e-4a99-becc-673e54f19856)

## Cart Edge Pins

Very briefly, I will categorize the different pins on the 32-pin cartridge edge connector.

- Pin 1 and pin 32 are VCC and GND, respectively.
- Pin 2 is the CLK or PHI pin, which is solely connected to the CLK pin on the MBC3 chip.
- Pin 3 is the /WR pin and pin 4 is the /RD pin. These signals partially determine when data is read from the ROM or when data is read/written to the RAM.
- Pin 5 is the /CS pin, which is unused on MBC3 carts.
- Pins 6 through 21 are the address pins A0 through A15.
- Pins 22 through 29 are the data pins D0 through D7.
- Pin 30 is the /RST (or reset) pin. When this pin is low, the Game Boy will cease operation.
- Pin 31 is a generally unused pin in Game Boy cartridges, but here it is connected to the EEPROM's /WE pin for compatibility with the GBxCART RW.

## MBC3 - Memory Bank Controller

The MBC3 is a memory mapping chip, used to expand the addressable memory space of a Game Boy cartridge. The RA14-RA20 and AA13-AA14 outputs are used to access higher memory banks on the ROM and RAM chips. It also has a positive and negative RAM chip select output to control data access on the RAM, though only the RAM /CS output is commonly used. The main upgrade over the MBC1 other than addressable memory space is the addition of a real time clock, or RTC, for time-based games like Pokemon GSC and Mary Kate and Ashley's Pocket Planner. The downside to the RTC is that it *greatly* increases the current draw of the MBC3 when operating on battery power, which is why your older Pokemon Red might still be holding a save, while your Pokemon Silver died a decade ago.

Further sections will provide more context for some of the MBC3 pins and the logic of how they are connected.

## ROM and RAM

The connections to the address and data pins here are mostly self-explanatory. However, the upper address pins on the ROM and RAM chips are controlled, or "banked", by the MBC3.
- A14 through A20 on the ROM chip are controlled by the RA14 to RA20 outputs from the MBC3.
- A13 and A14 on the RAM chip are controlled by the AA13 and AA14 outputs from the MBC3.
  - SJ5 and SJ6 are solder jumpers that must be set to match the size of the SRAM chip you're using. See the main README for information. The function of these jumpers is mostly to set a 64K SRAM chip's CE2 pin to the correct location for proper control (more information in later sections). 
 
Other pins include:

- The ROM's /CE pin is controlled by the A15 address pin and the /OE pin is controlled by the /RD pin on the cart edge (pin 4).
- The ROM's /WE pin, which was not on original cartridges, is connected to pin 31 on the cart edge for programming via the GBxCART RW as previously mentioned.
- The RAM's /OE pin is connected to the /RD pin on the cart edge (pin 4).
- The RAM's /WE pin is connected to /WR on the cart edge (pin 3).
- The RAM's /CE pin requires a bit of explanation, which will be covered in a later section.

## Battery Management and Data Retention Considerations

Ensuring current coming out of the battery is as minimal as possible while the Game Boy is turned off is crucial for long-lasting save data. The higher the current, the lower the battery life! But we need to do this without breaking compatibility with normal operation. So here are the things we need to keep in mind:

1) SRAM must have continuous power to retain save data.

2) Data on the SRAM must be accessible during normal gameplay.

3) The number of battery-powered current-consuming components should be minimized, and those that are powered must use as little current as possible.

4) Operation while the voltage is ramping down after turning off the power switch must be restricted to prevent junk data from being written to cartridge RAM.

Original MBC3 carts manage all of this with U4 - MM1026/BA6129, or MM1134/BA6375. I'll go over the functional requirements here, and for convenience, I'll be using the MM numbers when talking about them.

### SRAM Operational Background

For most Game Boy games, there were two main sizes of SRAM chips used - 64 Kbit and 256 Kbit (further refered to as just 64K or 256K). The pinouts of these two types of chips are nearly identical: 

![image](https://github.com/MouseBiteLabs/Game-Boy-MBC1-Cartridge/assets/97127539/3d5b20d1-d4e6-4598-b218-566d6fefaa61)

The big differences are the number of address pins and the number of chip enable pins. The 64K chip has 13 address pins and two chip enables (/CE and CE2), and the 256K chip has 15 address pins and one chip enable (just /CE). The additional address pins on the 256K SRAM gives it more memory, but one of the chip enable pins had to be sacrificed to accommodate this. This is kind of important, because the chip enable pins influence two main things - they tell the chip when data is being accessed, *and* how much current the chip is drawing during standby. For 64K SRAM chips, that's pretty easy - use one chip enable pin for data access, and the other for data retention mode. But for 256K SRAM, you have to do both of those functions at once. Look at the requirements for the low "data retention current" on the AS6C6264 datasheet and the AS6C62256 datasheet:

![image](https://github.com/MouseBiteLabs/Game-Boy-MBC1-Cartridge/assets/97127539/40d23786-5e9e-480f-b8f8-28d90928e621)

For 64K, /CE needs to be at VCC, *or* CE2 needs to be at GND for the current to be minimized. For 256K, /CE needs to be at VCC, full stop (in this case, VCC is whatever voltage appears on the SRAM's VCC pin, so when power is off that's the battery voltage). That's not super convenient if the power is off - something needs to make the /CE pin go to VCC, which means whatever that something is better not be pulling a lot of current, lest we lower our battery life.

On original MBC3 cartridges, by my findings after looking at cart images on gbhwdb, on all boards except DMG-LFDN-01 the MBC3 is powered via the battery management IC along with the SRAM. This is a common occurance because MBC3 games utilized a real-time clock, which needs the MBC3 to be powered while out of the Game Boy to retain the time. Since the MBC3 is powered by the battery, this means the MBC3 can provide the signals necessary to drive the CE2 or /CE pins on the SRAM to ensure low power draw while idling. The battery management IC, U4, is used to keep the /RESET and the /WP (or /SHDN) low while power is off.

For the LFDN-01 board where the MBC3 is powered by the 5V supply only (it doesn't use the RTC function), the /CE pin of the 256K SRAM on-board is gated by a NAND to keep it high while power is off - the inputs to the NAND device are pin 21 on the MBC3 (implied to be positive RAM CE output) and pin 3 of the BA6129, the CS output which is high during regular gameplay and GND when the game is off.

On my custom MBC3 board, I include compatibility with the MM and BA chips, as well as an option with completely new off-the-shelf components without the need for a donor. But before covering that, let's talk about the other important part of the battery backup system - how to properly and safely manage shifting between console power to battery power.

### Reset IC - MM1026

When you turn the power switch off on a DMG, the voltage on the VCC supply will ramp down. However, the DMG CPU requires 5 V to operate properly. A sufficiently lower voltage supplying the CPU may cause erroneous operation on the I/O pins of the CPU, even for a brief period of time. This can corrupt data stored on the SRAM of the game cartridge, say if the CPU accidentally writes junk data to part of the memory. This is bad! 

In order to prevent this, cartridges that have battery-backed SRAM on them use the battery management chip U4 to shut down operation of the CPU on pin 30 of the cart edge - the /RST (or /RESET) line. Asserting this to GND will stop all operation on the I/O pins, preventing corruption from occuring. According to the MM1026 datasheet, the /RESET output and CS output pins will be GND whenever voltage on the VCC pin is below 4.2V.

*Please note that the MM1026 has a /RESET output pin, but this is not the same as the Game Boy CPU /RESET input, the MBC3 /RESET input, or the cart edge /RST pin 30! I will call the MM1026's pin 2 the "Reset output pin" from now on for clarity.*

![image](https://github.com/MouseBiteLabs/Game-Boy-MBC1-Cartridge/assets/97127539/c3d14c7a-4695-4fc1-92df-16f425d90c79)

But that's not all the MM chip does. It also is used to pull the MBC3's /RESET input to GND as well. This is especially important as keeping this pin off will keep current draw of the MBC3 to a minimum. Furthermore, pulling this pin to GND also causes the MBC3 to assert the SRAM's /CE pin high to keep the SRAM in a low power state.

### Reset IC - MM1134

The MM1134 is nearly identical to the MM1026, with one distinct difference. There is one extra input that was previously an unconnected pin on the MM1026 - the /Y input. This input is intended to be used with a RAM /CE signal. It controls the /CS output (pin 5) on the MM1134. See the schematic and timing diagram below:

![image](https://github.com/MouseBiteLabs/Game-Boy-MBC1-Cartridge/assets/97127539/8d95eb57-2370-415c-8ff0-402ed947fdcd)

Essentially, the /CS output will follow the /Y input, *unless* VCC is below the 4.2V power-off threshold. Then, /CS will be pulled to VOUT (the combination of VCC and VBAT). Hey, this is great, because it means we can control that pesky 256K SRAM /CE pin *and* put it in a low power state when the power shuts off!

*Note: If you ground the /Y input, then the MM1134 will act exactly like an MM1026.*

From looking at carts on gbhwdb, it seems like this distinctive difference of the MM1134 vs. the MM1026 is unused. As the MBC3 is usually powered on the battery anyway, this functionality is fulfilled by the MBC3 itself.

### Open Collector Requirements on CPU /RESET Input

One minor, but important, note that drives a few design choices - there are actually two outputs on the MM1026/MM134 that go low when power is below 4.2 V. One of them is open collector (pin 2, the "Reset output"), and one is instead driven high by an internal transistor or pulled low by an internal pull-down (pin 3, the "CS output"). This is important because it means *you cannot safely use the CS output pin to control the Game Boy's /RESET line.*

![image](https://github.com/MouseBiteLabs/Game-Boy-MBC1-Cartridge/assets/97127539/5fb78360-aba5-40cd-b881-bf815daa9ee1)

On the DMG, the CPU's /RESET input is also connected to half of the power switch. When the switch is fully turned off, the /RESET input is pulled to GND directly.

![image](https://github.com/MouseBiteLabs/Game-Boy-MBC1-Cartridge/assets/97127539/225ab129-f5d6-421e-90c0-88530c2a48d6)

*[Image adapted from <a href="https://github.com/Gekkio/gb-schematics/blob/main/DMG-CPU-06/schematic/DMG-CPU-06.pdf">gekkio's DMG CPU board schematic</a>]*

This means that to ensure no damage is done to the battery management chip, any output connected to the Game Boy's /RESET input must be **open collector**. Open collector outputs can either float their output, or (in this case) pull it to GND - reference the "Pin 2 Reset Output" schematic above. That means pulling it to GND with the power switch won't damage anything internal to the chip, it will just safely bypass the pin's function.

But, in the case of pin 3 on the MM chips, the "CS output" pin, imagine a scenario where the power switch was turned off, but the capacitors on the board have not yet discharged down to 0V. This means there'd still be some residual voltage on the VCC net. But, the CS output pin is connected to VCC via the transistor internal to the chip, and if VCC hasn't dropped far enough, then this would still be pulled high to VCC. So if you had connected pin 3 to the /RESET input on the Game Boy, in this scenario, there would be a path from VCC on the MM's supply pin, *through* the transistor on pin 3, through the power switch on the DMG, to GND - short circuiting the capacitors on the board. This means that for a moment, the internal transistor driving pin 3 would experience a surge of current. Since the output is not designed to do that, it could cause damage to the part. So let's avoid that situation!

In conclusion, any output connected to the /RESET net on the Game Boy *must* be open collector.

*On the GBC, they added a chip that pulls the /RESET line to GND through an open collector output whenever the voltage on VCC drops below 3.5 V, instead of using the power switch to ground the input. If the DMG had this, maybe Nintendo wouldn't have needed these MM chips but could use something a bit simpler. But we must design cartridges with DMG compatibility in mind!* 

## Battery Management Implementation Options

Ok, with the background taken care of, I'll talk about how my MBC3 board manages all of this, and the different compatibility options. If you reference the schematic above, you will see two options for games that use SRAM:
1) MM1026 or MM1134 ("Group A" components)
3) No donor MM chip ("Group B" components)

### Using an MM Chip

For reference again, here is a pinout diagram of the MM1026 and MM1134.

![image](https://github.com/MouseBiteLabs/Game-Boy-MBC1-Cartridge/assets/97127539/fd0f15fb-96d5-4d6b-a1fa-3b0cd7466f58)

And here is the section of the schematic for using one of these chips ("Group A" components).

![image](https://github.com/MouseBiteLabs/Game-Boy-MBC3-Cartridge/assets/97127539/fca06a50-b38e-427b-a17a-fd0aa426f217)

#### Creating the Battery-Backed Power Supply

The VOUT pin is a combination of the VCC and VBAT pins. VCC is connected to the Game Boy's 5V supply, and VBAT is connected to the battery (through a current-limiting resistor). When VCC drops below 3.3V during power-down, the MM chip switches VOUT to be supplied by VBAT instead of VCC. Conversely, when it passes above 3.3V during power-on, the MM chip switches VOUT to be supplied by VCC instead. This guarantees constant power to the SRAM to keep save data retained at all times, assuming the battery is not dead.

#### Reset Pin Management

Because anything connected to the /RESET input pin on the cart edge must be open collector, the open collector output of the MM chip, pin 2, is connected to the /RESET cart edge pin (pin 30). The MBC's /RESET input, pin 4, can be connected one of two ways, based on how SJ3 is soldered.

![image](https://github.com/MouseBiteLabs/Game-Boy-MBC3-Cartridge/assets/97127539/41b15826-873f-4dcc-a424-c6c6f22047be)

For revision-less MBC3 chips, it's recommended to populate Q1 and bridge SJ3 to route the MBC3's /RESET input to the cart edge pin 30 (net label /RST). Q1's gate is driven by pin 5 output of the MM chip (net CSB), which is high whenever VCC falls below 4.2V. This essentially actively keeps the /RESET line low when power is off, and allows the console to assert the /RESET input of the MBC3. This FET circuit is seen on DMG-KECN-SP boards, which use a revision-less MBC3 boards. The reason this wiring is recommended is unclear - KECN-01 boards use revision-less MBC3 chips and do not include it - but to be safe, it should be added. 

Boards that use MBC3A or B do not have this extra FET, and instead drive their /RESET inputs with the CS output of the MM chip, pin 3.

#### Controlling the SRAM /CE Pin

As mentioned earlier, since the MBC3 is powered on the battery, the RAM /CS output pin can keep the SRAM in a low power state while power is off.

### Using Brand New Components

If you do not have one of these MM chips, as you can only typically get them from donor Game Boy cartridges and not all games use them, you can achieve the same results using commercially-available components. In order to do so, you need to populate "Group B" components which features the TPS3613 - a Texas Instruments chip that is almost identical to the MM1134, with a few minor differences. You also need to solder Q1 onto the board, regardless of your MBC3 revision.

![image](https://github.com/MouseBiteLabs/Game-Boy-MBC3-Cartridge/assets/97127539/c9912031-4962-4de5-bdf1-28e1f9c5e096)

Here is the timing diagram from the TPS3613 datasheet, showing the relations between the inputs and outputs of the chip.

![image](https://github.com/MouseBiteLabs/Game-Boy-MBC1-Cartridge/assets/97127539/6c6149f9-d40f-4ec4-b151-b59f4dcc069b)

#### Creating the Battery-Backed Power Supply

Like the MM chips, VOUT is created by VCC or VBAT. But instead of a hard internal threshold, the output of this pin is determined by the voltage on the SENSE pin. If the voltage on SENSE is above an internal set voltage of 1.15 V (shown as VIT in the diagram), VOUT is equivalent to VCC. When the SENSE voltage drops below VIT, and when VCC drops below VBAT, VOUT switches over to being powered by VBAT instead.

The voltage on SENSE is determined by the voltage divider made with R9 and R10. The MM chips have an internal threshold of 4.2 V for determining that the power is going off and the /RESET outputs should be asserted to protect the RAM from spurious writes. So for this design, I'll stick to that threshold.

#### Reset Pin Management

The /RESET and RESET outputs of the TPS3613 are push-pull. This is slightly inconvenient, because it means you cannot directly connect them to the /RST pin on the cart edge (pin 30), because as discussed earlier, any connection to this pin must be open collector. So, we just have to make it open collector (or in this case, open drain). Adding Q1, an N-channel MOSFET, achieves this.

- When voltage on the SENSE pin is above the internal 1.15V threshold, the /RESET output pin is asserted high (keeping pin 25 on the MBC3 high), and the RESET output pin is asserted low. The low positive RESET output will keep Q1 off and allow /RST, pin 30 on the cart edge, to be pulled high by the Game Boy internals and allow operation.
- When voltage on the SENSE pin drops below the internal 1.15V threshold, the /RESET output pin is asserted low turning off the MBC3, and the RESET output pin is asserted high. This will turn Q1 on, and pull pin 30 on the cart edge to GND. Note that the positive RESET output follows the voltage on the VDD pin, so once the 5V supply on the Game Boy depletes, Q1 will be off again.
  - The consequence of this is you should use an MBC3A or MBC3B to make games with the TPS3613. As mentioned earlier, revision-less MBC3 chips should be connected such that the /RESET input is connected to cart edge pin 30, and driven by Q1. Because the positive RESET output eventually goes to zero, Q1 will eventually cease to conduct, leaving the MBC3's /RESET input pin floating. This causes excess power draw of the MBC3, which will kill your battery much faster than normal. You need to use the driven /RESET output of the TPS3613 to keep the /RESET input pin of the MBC3 at GND at all times.

#### Controlling the SRAM /CE Pin

The /CEIN input and /CEOUT output pins of the TPS3613 acts exactly like the /Y input and /CS output pins of the MM1134 - /CEOUT will follow /CEIN as long as the voltage on the SENSE pin is above 1.15V. This feature is unused on the MBC3 cart because, again, the MBC3 is powered via the battery which can assert the RAM's /CS pin to keep it in a low power mode.

## Estimating Battery Life

You can get a very rough estimate of battery life by measuring the voltage in millivolts across the battery-series resistor R1. Just use Ohm's Law to find the current: I = V / R where V is the voltage in millivolts you measure and R is the resistance of R1 in ohms. If you use these units, the current will be in milliamps. 

*Fun fact: you can measure this voltage using TP2 and TP3 on the back of the board as your multimeter probe points.*

Then, find the milliamp-hour rating of your selected battery (preferrably from a datasheet). For example, a Renata CR2032 battery is rated for 225 mAh. Take this number and divide by the milliamps calculated above, to get a rough estimate of the number of hours the battery can supply when the console is turned off (when it is on, the Game Boy powers the cart so the battery is unused). Divide by 24 and then 365 (or just divide by 8760) and you will get a number of years. 

So for an overall equation to determine the very approximate number of years the battery will survive:

Years = Resistance of R1 (ohms) * Battery capacity (mAh) / Voltage (mV) / 8760 

For an example: an MBC3 cartridge where R1 is 10 kΩ (or 10000 Ω), using a CR2032 rated for 230 mAh, and a voltage of 20 mV measured across the terminals of R1, yields 10000 * 225 / 40 / 8760 = 12.84 years of battery survival. This number can be different due to changing environmental conditions, self-discharge of the battery, and also actual capacity of the battery - discharging a battery at very small currents will increase its apparent capacity, and as the voltage decreases, the current required for data retention decreases as well.

Just backup your save data within a decade of making the cart and you'll be fine.

## A Few Extra Capacitors

I added spots for a few extra capacitors, in the event some compatibility issues arise. By default, you should ignore these components, as they were not present in most cartridges. If I find instances where they are needed, I will note them in this repo. For now, assume they are unnecessary.

![image](https://github.com/MouseBiteLabs/Game-Boy-MBC3-Cartridge/assets/97127539/48f03643-dedd-4fe4-8d8a-9458f3a89dd4)

1) C9 is a capacitor connected nearby the MBC3's /WR pin to GND. These were populated on some cartridges, and have a value of approximately 1 nF (0.001 uF).
2) C10 is a capacitor connected nearby the MBC3's /RESET pin to GND. A spot for this capacitor was included on some MBC1 carts, but I cannot locate an instance of it actually being used when looking through the gbhwdb. I don't actually know what value this capacitor would need to be, so I am guessing 1 nF.
3) C11 is a capacitor that I have added that was not used on any original Game Boy board. It connects to the SRAM's /CE pin, bypassing to GND. I have added this in the event incompatibilities arise with newer SRAM chips that have much faster speeds than older SRAM chips. This was a problem I encountered on some of my NES cartridges, so this is here just in case.

## Resources

- <a href="http://www.devrs.com/gb/files/hardware.html">Jeff Frohwein's GameBoy Tech Page</a>
- <a href="https://gbhwdb.gekkio.fi/">Game Boy Hardware Database</a>
- <a href="https://catskull.net/gb-rom-database/">Nintendo Gameboy Game List</a>
- <a href="https://www.gbxcart.com/">insideGadgets discord server for GBxCart RW compatibility requirements</a>
- <a href="https://www.alldatasheet.com/datasheet-pdf/pdf/99104/MITSUBISHI/MM1026.html">System Reset IC Datasheet</a>
- <a href="https://www.ti.com/lit/ds/symlink/tps3613-01.pdf?HQS=dis-mous-null-mousermode-dsf-pf-null-wwe&ts=1698238885366&ref_url=https%253A%252F%252Feu.mouser.com%252F">TPS3613 Datasheet</a>
- <a href="https://www.alliancememory.com/wp-content/uploads/pdf/Alliance%20Memory_64K_AS6C6264v2.0July2017.pdf">AS6C6264 Datasheet</a>
- <a href="https://www.alliancememory.com/wp-content/uploads/pdf/AS6C62256.pdf">AS6C62256 Datasheet</a>
- <a href="https://github.com/Gekkio/gb-schematics/blob/main/DMG-CPU-06/schematic/DMG-CPU-06.pdf">Gekkio's Game Boy Schematic Resources</a>

## License
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/80x15.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>. You are able to copy and redistribute the material in any medium or format, as well as remix, transform, or build upon the material for any purpose (even commercial) - but you **must** give appropriate credit, provide a link to the license, and indicate if any changes were made.

©MouseBiteLabs 2023
