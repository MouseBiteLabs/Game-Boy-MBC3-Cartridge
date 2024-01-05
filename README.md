# Game Boy MBC3 Cartridge

This is my design of a flashable MBC3-based cartridge for the Game Boy. The MBC3 mapper improves a lot over the MBC1, and includes a real-time clock for everyone's favorite battery-sucking game.... Mary Kate and Ashley Pocket Planner! Oh, and Pokemon.

This circuit board should cover most, if not all, MBC3 games. The features are as follows:

- Able to make games up to 2 MB in size, that use up to 256 Kbit of RAM
- Compatibility with all four of the popular Game Boy battery management ICs - MM1026, MM1134, BA6129, and BA6735
- The option to add battery backup to the cartridge *without* the need of the original battery management ICs
- Fully compatible with the <a href="https://www.gbxcart.com/">GBxCart RW</a> so you can transfer games and save files to and from the board
- 
![assemblepic](https://github.com/MouseBiteLabs/Game-Boy-MBC3-Cartridge/assets/97127539/45211af2-9ab5-4f83-98d1-424d3adb0a3d)

![boardscan3](https://github.com/MouseBiteLabs/Game-Boy-MBC3-Cartridge/assets/97127539/a80373fc-9791-403f-a3d8-92dc1ae45699)

All gerbers and source files can be found in this repo, as this project is fully open source. Technical documentation of the board can be found in the Technical folder.

## Important Things Before You Start

1) To use this board, you need to have an original Game Boy game that uses an MBC3 mapper chip. <a href="https://catskull.net/gb-rom-database/">You can find a list of games and their mappers here</a>. Use the search function.
2) You will need to remove the MBC3 from your donor cartridge for use on this board. This will require a hot air rework station or a hot plate. There's a list below of other parts you can re-use from the donor cartridge.
3) When soldering parts on, it's a good idea to put kapton tape or otherwise cover the bottom cartridge edge. You do not want to get solder on the cartridge contacts.
4) I am not responsible for any damage you do to your self or your property. I do not guarantee design compatibility. You may encounter issues with certain games! Attempt this project at your own risk.
5) If you are using this board to make games other than for personal use, you must have permission from the originator to use and distribute any ROM images or other related material. You are responsible for making sure you adhere to any license requirements.

DO NOT use my circuit boards for profiting from stolen work - this especially includes homebrew content, ROM hacks, and using fan-made labels without permission from the originator. **Support original creators!**

## Board Characteristics and Order Information

The zipped folder contains all the gerber files for this board. The following options **must** be chosen when ordering boards for yourself.

- Thickness: 0.8mm
- Surface Finish: ENIG
- Gold Fingers: Yes, 30° chamfer

**Add this note to orders:** "The milling.gbr layer indicates where an unplated slot should be added to the board. Please add an unplated slot as indicated."

**I sell this blank circuit board on Etsy, so you don't have to buy a bunch of multiples if you don't want to.** (Click the banner!)

<a href="https://mousebitelabs.etsy.com/listing/1605239014/game-boy-cartridge-pcb-mbc3"><img src="https://github-production-user-asset-6210df.s3.amazonaws.com/97127539/239718536-5c9aefe3-0628-4434-b8d8-55ff80ac3bbc.png" alt="PCB from Etsy" /></a> 

You can use the zipped folder at any board fabricator you like. You may also buy the board from PCBWay using this link (disclosure: I receive 10% of the sale value to go twoards future PCB orders of my own):

<a href="https://www.pcbway.com/project/shareproject/Game_Boy_MBC3_Cartridge_a4f7b749.html"><img src="https://www.pcbway.com/project/img/images/frompcbway-1220.png" alt="PCB from PCBWay" /></a>

<a href="https://oshpark.com/shared_projects/6rPI4Bk8">The board is also listed on OSH Park as well.</a> **Be sure to get them in 0.8mm thickness if you order from here.**

## Required Equipment

The EEPROMs on the board needs to be programmed somehow. I recommend using the GBxCart, as mentioned. These boards are fully compatible with it, and it makes reflashing games extremely easy using <a href="https://github.com/lesserkuma/FlashGBX">lesserkuma's FlashGBX software</a>.

Alternatively, you can buy an EEPROM programmer with a TSOP adapter. The downside to this method is that you have to desolder the chip every time you want to program it. The <a href="https://flashcatusb.com/">FlashcatUSB</a> is one popular option in retro spaces.

## Battery Safety

When assembling a board, I recommend populating all the parts *except* the battery, and getting it to run initially without it. This is to make it easier to fix any solder connections that might need fixing, without having to worry about getting the battery hot. And if you need to rework anything near the battery after you've put it on the board, be safe and remove it before putting a hot soldering iron next to it.

And this should go without saying, but if you're assembling these boards with a hot plate or hot air, *do not* solder the battery on this way. You should use an iron, and keep heat off of the battery as much as possible. 

(Also check polarity!)

## Board Configurations

The board comes with five sets of jumper pads for solder bridges. SJ3, SJ5, and SJ6 require you to solder bridge the middle pad either to the left or right pads. SJ1 and SJ4 are configured by either leaving them alone or bridging them with solder. Here are the situations where you need to add solder bridges.

### Making Games Without SRAM or Battery (SJ1 and SJ4)

Solder bridge SJ1 and SJ4 if your game does not use any SRAM or battery. I don't believe there were ever any MBC3 games made *without* SRAM, but if you wanted to... remove it for some reason, you can.

### MBC3 Type Select (SJ3)

The MBC3 chip you use from the donor cartridge can be one of a few different types:

- If your MBC3 doesn't have a revision letter on it (it just says "MBC3" on it) then bridge the middle pad of SJ3 to the left. (You will also need to populate Q1!)
  - If you're using a revision-less MBC3, and are making a game that requires a battery, you should use a donor MM1026, MM1134, BA6129, or BA6735 battery management IC instead of the replacement TPS3613.
- If your MBC3 *does* have a revision letter, such as MBC3A or MBC3B, then bridge the middle pad of SJ3 to the right.

![image](https://github.com/MouseBiteLabs/Game-Boy-MBC3-Cartridge/assets/97127539/24aaa7eb-fbc7-428d-955a-e1005c491098)

*[Images of MBC3 chips from <a href="https://gbhwdb.gekkio.fi/cartridges/mbc3.html">Game Boy Hardware Database</a>.]*

### RAM Size Selection (SJ5 and SJ6, or SW1)

These jumpers are located underneath the MBC3 chip, and labeled "64K" and "256K". Solder them to configure the amount of RAM your cart uses. You must configure these pads for every game you make, unless you do not need RAM. <a href="https://catskull.net/gb-rom-database/">You can find a list of games here with their respective RAM sizes.</a>

- If the game you're making uses 256 Kbit of RAM, then solder the middle pads of the two jumper sets to the right pads.
- If the game you're making uses 64 Kbit of RAM, then solder the middle pads of the two jumper sets to the left pads.
- SJ5 and SJ6 must be soldered in the same direction.
- The footprint of these selection pads should allow for a DPDT switch, part number CAS-220A1, to be placed on these pads instead of having to bridge the pads with solder.

Note that you can make games that only require 64 Kbit of RAM and still use a 256 Kbit SRAM chip. You still need to configure the jumpers to the 64 Kbit setting, though.

![image](https://github.com/MouseBiteLabs/Game-Boy-MBC3-Cartridge/assets/97127539/fe85ff2c-26b0-4e2a-8a7f-ffec7c7e6388)

### Games Without a Real-Time Clock

If your game does *not* use the RTC function with the crystal X1, then you need to bridge the pads of Z1 (which is called C3 on v1.3 and earlier).

![image](https://github.com/MouseBiteLabs/Game-Boy-MBC3-Cartridge/assets/97127539/d46ea632-6318-4e5d-bf40-c4e9a7f52aa1)

## Test Points and Final Checkout

On the back of the board are five test points. Here's where they are connected:

- TP1: SRAM supply voltage
- TP2: Battery voltage (after R1)
- TP3: Battery voltage (positive terminal of battery)
- TP4: Ground
- TP5: VCC input voltage

After you assemble your game, you should measure the current out of the battery. But first, you should program it with the GBxCart, or if you programmed the EEPROM separately, put it into a Game Boy and cycle power once. Then, flip the PCB upside down on a non-conductive surface (not your leg), and set your multimeter in DC millivolts (or volts). Put the positive probe on TP3 and the negative probe on TP2. If you used a 10kΩ for R1, as indicated in the BOM, you should read a voltage in the single or tens of millivolts for non-RTC games, or up to 40 mV for games using a real-time clock. If you have something much higher, especially voltages above 40mV, then you likely have an issue or short circuit on the board somewhere.

**Note: You need to power up the game at least once before battery currents will make sense - the battery management ICs can start up in an unknown state before applying main power to the board.**

To estimate battery life, see <a href="https://github.com/MouseBiteLabs/Game-Boy-MBC3-Cartridge/tree/main/Technical#estimating-battery-life">this section in the Technical Design Document</a>, or for more in-depth analysis, <a href="https://hackaday.io/project/11864-tritiled/log/72554-determining-maximum-runtime-176-to-202-years-cr2032">this Hackaday post</a>.

### Current Draw Measurements

The revision of MBC3 chip you are using will influence the current draw out of the battery when the game is off, and thus how long your battery life will last. Using the real-time clock function on the MBC3 will draw more current than if you do not.

For the test set up, I am replacing the battery with a regulated DC power supply set for 3 VDC for consistency, on a cart board with an MM1134 chip for U4, and brand new AS6C62256 SRAM. The "no RTC" measurements have Z1 (or C3) shorted, and the "with RTC" measurements have R2, C2, Z1, and X1 populated. I am using a Fluke 117 Multimeter in DC mV mode for measurements.

| Rev   | P/N      | Current draw (without RTC) | Battery Life Estimate (no RTC, CR2025) | Current draw (with RTC) | Battery Life Estimate (RTC, CR2025)  | Battery Life Estimate (RTC, CR2032) |
| ----- | -------- | -------------------------- | -------------------------------------- | ----------------------- | ------------------------------------ | ----------------------------------- |
| MBC3  | LR385364 |          0.2 uA            |               >50 years                |            1.8 uA       |               10 years               |            14 years                 |
| MBC3  | BU3631K  |          0.6 uA            |                31 years                |            1.6 uA       |               12 years               |            16 years                 |
| MBC3  | P-1      |          0.4 uA            |                47 years                |            3.8 uA       |               5 years                |             7 years                 |
| MBC3A | LR38536B |          0.2 uA            |               >50 years                |            1.5 uA       |               13 years               |            18 years                 |
| MBC3A | BU3632K  |          0.5 uA            |                37 years                |            1.5 uA       |               13 years               |            18 years                 |
| MBC3A | P-2      |          0.5 uA            |                37 years                |            3.9 uA       |               5 years                |            7 years                  |
| MBC3B | BU3634K  |          0.6 uA            |                31 years                |            1.5 uA       |               13 years               |            18 years                 |
| MBC3B | P-2      |          0.4 uA            |                47 years                |            3.7 uA       |               5 years                |            7 years                  |

*Note: The SRAM I used, AS6C62256, drew 0.05 uA by itself for these tests. Your current draw may vary depending on the SRAM you are using.*

### Why not FRAM?

This board is only suitable for using SRAM. One downside to SRAM, if you haven't figured it out yet, is that you need a battery to keep the SRAM powered on even when the game is turned off. So eventually the battery will die, and your save data is lost. Some people have used FRAM, or Ferroelectric RAM, to keep save data around even after the battery dies (specifically, the popular part that's used for FRAM carts is the FM18W08). But, using this FRAM chip has a handful of downsides, and the benefit of keeping save data after the battery dies *in my opinion* does not outweigh the cons, which are as follows:

1) Quality, new stock, 5V tolerant FRAM is expensive ($12+ per part).
2) Cheaper FRAM chips from eBay or Aliexpress are notoriously flaky (anecdotally, ~50% success rate).
3) FRAM memory access requires different chip select timing than SRAM, and the Game Boy expects SRAM, so it is not natively compatible with FRAM carts. The Gameboy Color specifically cannot (easily) be made to properly access FM18W08 chip. You *can* use an OR gate and add the CLK on the cart edge to try to achieve the necessary timing, but it only works (properly) for DMG/MGB/SGB, not the GBC. It usually *works* in practice for GBC, but it's technically a datasheet violation and can potentially cause issues in edge cases. <a href="https://github.com/Gekkio">(Thanks to gekkio for pointing this out!)</a>

Brand new SRAM chips are ~$3, and having the SRAM footprint also allows you to use SRAM from an original cart if desired. And other than the scenario where you are making a game with a real-time clock and are using an inefficient MBC3 variant (P-1 or P-2), you can easily get more than a decade of battery life. You're already assembling this cartridge; you can dump the save and replace the battery!

Note that this isn't to throw shade at any FRAM-based carts, but for me personally, it's more trouble than it's worth. If you just *need* FRAM, you can find other cart designs out there that have it, <a href="https://retrogamerepairshop.com/products/game-boy-2mb-32kb-fram-mbc3-with-rtc-flash-cartridge-by-hdr?variant=40144623927468">like this one!</a>

## Board Fitment

The shape of this board was originally meant to mimic original Game Boy circuit boards as closely as possible (v1.3 and earlier). Unfortunately, when placed in some aftermarket Game Boy cartridge shells (like those from Cloud Game Store), the circuit board has a lot of freedom to rotate around the main screw hole in the bottom-middle of the cartridge. This can cause misalignment when you put it in a Game Boy, which can cause a game to either not load properly (garbled Game Boy logo) or shut off the Game Boy because of a short circuit. This isn't *dangerous* or anything, just annoying.

In order to make the boards fit nicer in any kind of shell, for v1.4 I added extended tabs of circuit board material to the edges of the cartridges to keep it from rotating too much in shells, which was suggested to me by <a href="https://github.com/orangeglo">orangeglo</a>! (Thanks!)

If you're having trouble fitting the circuit board into a shell, because the tabs interfere with the cart edges, you can safely sand or trim them down as there is no copper within the tabs themselves. The only shell that appears to require any kind of trimming are Kitsch-Bent shells.

## Bill of Materials (BOM)

Your parts list will vary depending on the game you are trying to make, and what chips you have for the battery management (if any). Note that C9 - C11 footprints are only included for edge cases that may require them; you can ignore them unless you run into issues.

Please carefully review the parts you need for the board you are trying to make. Do not add any parts to your build that don't appear in the column for the game you are making. This means you *cannot* populate every component on the board at the same time.

**NOTE: If you are *not* using the RTC function of the MBC3, you need to bridge the pads of Z1 (or C3) with solder.**

| Reference Designators | Value/Part Number              | Package          | Description        | No save carts | Save carts with U4 | Save carts without U4 | Source                                           |
| --------------------- | ------------------------------ | ---------------- | ------------------ | ------------- | ------------------ | --------------------- | ------------------------------------------------ |
| B1                    | CR2032, CR2025, CR2016         | CR2032           | Backup Battery     |               | x                  | x                     | [https://mou.sr/3SeAzfT](https://mou.sr/3SeAzfT) |
| C1                    | 0.1uF                          | 0603             | Capacitor (MLCC)   | x             | x                  | x                     | [https://mou.sr/3ENc15O](https://mou.sr/3ENc15O) |
| C2                    | 15pF                           | 0603             | Capacitor (MLCC)   |               | If using RTC       | If using RTC          | https://mou.sr/3PPorjO                           |
| Z1 (or C3)            | 15pF                           | 0603             | Capacitor (MLCC)   |               | If using RTC       | If using RTC          | https://mou.sr/3PPorjO                           |
| C4                    | 0.1uF                          | 0603             | Capacitor (MLCC)   |               | x                  |                       | [https://mou.sr/3ENc15O](https://mou.sr/3ENc15O) |
| C5                    | 0.1uF                          | 0603             | Capacitor (MLCC)   |               | x                  | x                     | [https://mou.sr/3ENc15O](https://mou.sr/3ENc15O) |
| C6                    | 10uF                           | 0603             | Capacitor (MLCC)   |               | x                  | x                     | [https://mou.sr/3mZtSkF](https://mou.sr/3mZtSkF) |
| C7                    | 0.1uF                          | 0603             | Capacitor (MLCC)   | x             | x                  | x                     | [https://mou.sr/3ENc15O](https://mou.sr/3ENc15O) |
| C8                    | 0.1uF                          | 0603             | Capacitor (MLCC)   |               |                    | x                     | [https://mou.sr/3ENc15O](https://mou.sr/3ENc15O) |
| Q1                    | 2N7002                         | SOT-23           | N-Channel FET      |               | If MBC3 no rev     | x                     | [https://mou.sr/3rgfh6J](https://mou.sr/3rgfh6J) |
| R1                    | 10k                            | 0603             | Resistor           |               | x                  | x                     | [https://mou.sr/3riR7IH](https://mou.sr/3riR7IH) |
| R2                    | 330k                           | 0603             | Resistor           |               | If using RTC       | If using RTC          | [https://mou.sr/3PZ2pvj](https://mou.sr/3PZ2pvj) |
| R8                    | 10k                            | 0603             | Resistor           | x             | x                  | x                     | https://mou.sr/3riR7IH                           |
| R9                    | 130k                           | 0603             | Resistor           |               |                    | x                     | [https://mou.sr/3MjXliy](https://mou.sr/3MjXliy) |
| R10                   | 49.9k                          | 0603             | Resistor           |               |                    | x                     | https://mou.sr/3Q3NRZO                           |
| U1                    | 29F016, 29F032, 29F033         | TSOP-48, TSOP-40 | Flash EEPROM       | x             | x                  | x                     | AliExpress or eBay                               |
| U2                    | MBC3                           | QFP-32           | MBC3 Mapper        | x             | x                  | x                     | Donor MBC3 Game Boy cartridge                    |
| U3                    | AS6C6264, AS6C62256            | SOP-28           | SRAM               |               | x                  | x                     | [https://mou.sr/450klcY](https://mou.sr/450klcY) |
| U4                    | MM1026, MM1134, BA6129, BA6735 | SOIC-8           | Battery Management |               | x                  |                       | Donor Game Boy cartridge                         |
| U5                    | TPS3613                        | MSOP-10          | Battery Management |               |                    | x                     | [https://mou.sr/45Ir2kh](https://mou.sr/45Ir2kh) |
| X1                    | 32.768 kHz                     | Radial           | Crystal Oscillator |               | If using RTC       | If using RTC          | https://mou.sr/3ZteKuy                           |

### Usable Donor Cartridge Parts

You can use a few parts from the donor cart on the new board to save some money. Note that you will generally get better reliability with new parts as opposed to old ones. For example: I have seen failed RAM chips from donors in the past.

1) **C1/Z1, C2, R2, X1: RTC Components** - You can move over these parts if you're using the real-time clock function. The two capacitors are the same value, so they are interchangeable. On games that *don't* use the RTC, you can't move "Z1" over, because it's a resistor on those boards. Also be careful that on my board, the RTC capacitors are designated as "Z1" (or "C3") and "C2" - don't accidentally move the wrong capacitors over from the donor board to these spots!
2) **U2: MBC3** - This one is required
3) **U3: SRAM** - You can use this part *only if* the game you're making uses the same or less amount of RAM that the donor cartridge does
4) **U4: Battery Management IC** - Using this is probably preferred over the TPS3613 because it'll save you money and parts to put on

You could probably transfer over most of the 0.1uF capacitors but they're pretty cheap anyway, so I generally just recommend buying new resistors and capacitors.

### Capacitors C9 to C11

If you want to experiment with the capacitors C9 to C11, see this info below. **Be aware that adding these components in some of my tests actually ***stopped*** games from working, so again, assume that if you have a problem it lies elsewhere first, because it almost certainly does.**
- C9 is a capacitor connected nearby the SRAM's /WE pin to GND. These were populated on some cartridges, and I believe have a value of approximately 1 nF (0.001 uF) based on measurements from an MBC1 board that used it.
- C10 is a capacitor connected from the cart edge /RESET pin to GND. A spot for this capacitor was included on some MBC1 carts, but I cannot locate an instance of it actually being used when looking through the gbhwdb. It does not appear on *any* MBC3 boards. I don't actually know what value this capacitor would need to be, so I am guessing 1 nF.
- C11 is a capacitor that I have added that was not used on any original Game Boy board. It connects to the SRAM's /CE pin, bypassing to GND. I have added this in the event incompatibilities arise with newer SRAM chips that have much faster speeds than older SRAM chips. This was a problem I encountered on some of my NES cartridges, so this is here just in case, but I have not found an instance where it's required.

## Things to Remember

- The footprint for the EEPROM is specifically for 29F016 - it has 48 pins. However, 29F032 and 29F033 are only 40 pin devices. They still work fine on the board though - place them in the center of the footprint, and leave the outer two pins on each corner empty
- The 29F016, 29F032, and 29F033 have been known to occasionally be defective upon arrival. They're either used, or new old stock, and usually only available from AliExpress.
- The footprint for the battery can fit a CR2032, CR2025, or CR2016 with solder tabs. The only difference is the mAh capacity (larger number = longer life). If you get Panasonic tabbed batteries, you may have to trim the battery tabs to make them fit on the footprint.
  - For untabbed coin cells, you can find battery retainer adapters online, <a href="https://retrogamerepairshop.com/products/hdr-game-boy-game-battery-retainer?variant=40511013290156">like this one,</a> but only for board revision v1.4. It will not fit on earlier revisions.
- For battery management, use either U4 *or* U5 and supporting components. **Do not** use U4 and U5 simultaneously on one board. They will interfere with each other.
- Generally, ROM sizes are conveyed in terms of kilo**bytes** (KB) and mega**bytes** (KB, MB). RAM size is usually conveyed in terms of kilo**bits** or mega**bits** (Kbit, Mbit). You can convert Kbit and Mbit to KB and MB by dividing Kbit or Mbit by 8. For example, 256 Kbit = 32 KB.
- You only need to provide ROM and RAM chips that have at least *or greater* the size of the game you are trying to make. That means you can use a 256 Kbit SRAM chip for a game that only requires 64 Kbit of RAM!

## Revision History

### v1.4
- Changed "C3" to "Z1"
- Reduced cart edge by 0.1 mm for better fitment
- Adjusted battery placement to support more battery holders
- Added tabs on the board edges to allow the circuit board to fit better in aftermarket cartridge shells

### v1.3
- Extended cart edge down by 0.25 mm for better fitment
- Added OSHW logo and "SUPPORT ORIGINAL CREATORS!"

### v1.2
- Replace non-donor battery management circuitry with a TPS3613-based circuit for smaller BOM and easier routing

### v1.1
- Moved all parts on the top down to allow for compatibility with DMG-style shells
- Rotated battery for more space
- Widen SRAM footprint for easier soldering
- Renamed some reference designators for consistency between designs
- Changed silkscreen for clarity

### v1.0
- Prototype revision

## Resources and Acknowledgements

- <a href="http://www.devrs.com/gb/files/hardware.html">Jeff Frohwein's GameBoy Tech Page</a>
- <a href="https://gbhwdb.gekkio.fi/">Game Boy Hardware Database</a>
- <a href="https://catskull.net/gb-rom-database/">Nintendo Gameboy Game List</a>
- <a href="https://www.gbxcart.com/">insideGadgets discord server for GBxCart RW compatibility requirements</a>
- <a href="https://github.com/lesserkuma/FlashGBX">Lesserkuma's FlashGBX software</a>
- <a href="https://www.alldatasheet.com/datasheet-pdf/pdf/99104/MITSUBISHI/MM1026.html">System Reset IC Datasheet</a>
- <a href="https://www.ti.com/lit/ds/symlink/tps3613-01.pdf?HQS=dis-mous-null-mousermode-dsf-pf-null-wwe&ts=1698238885366&ref_url=https%253A%252F%252Feu.mouser.com%252F">TPS3613 Datasheet</a>
- Board outline modified from <a href="https://tinkerer.us/projects/homebrew-gameboy-cartridge.html">Dillon Nichols's Homebrew Gameboy Cartridge project</a>
- Thanks to <a href="https://github.com/orangeglo">orangeglo</a> for his suggestion of adding spacers around the board edge for better fitment in aftermarket cartridges
- Some components from <a href="https://github.com/adafruit/Adafruit-Eagle-Library">Adafruit's Eagle parts library</a>
- Some components from <a href="https://www.snapeda.com/">SnapMagic</a>
- Thank you to <a href="https://github.com/Gekkio">gekkio</a> for their deep Game Boy knowledge resources, and for collaboration in demystifying some of the design choices on Game Boy cartridges
- Thanks to the awesome members of the <a href="https://moddedgameboy.club/">Modded Gameboy Club</a> for their feedback and support during the entire project development

## License

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/80x15.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>. You are able to copy and redistribute the material in any medium or format, as well as remix, transform, or build upon the material for any purpose (even commercial) - but you **must** give appropriate credit, provide a link to the license, and indicate if any changes were made.

©MouseBiteLabs 2023
