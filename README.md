# Game Boy MBC3 Cartridge

This is my design of a flashable MBC3-based cartridge for the Game Boy. The MBC3 mapper improves a lot over the MBC1, and includes a real-time clock for everyone's favorite battery-sucking game.... Mary Kate and Ashley Pocket Planner! Oh, and Pokemon.

This circuit board should cover most, if not all, MBC3 games. The features are as follows:

- Able to make games up to 16 Mb in size, that use up to 256 Kb of RAM
- Compatibility with all four of the popular Game Boy battery management ICs - MM1026, MM1134, BA6129, and BA6735
- The option to add battery backup to the cartridge *without* the need of the original battery management ICs - perfect for MBC3 donors that didn't have batteries in them
- Fully compatible with the <a href="https://www.gbxcart.com/">GBxCart RW</a> so you can transfer games and save files to and from the board

![image](https://github.com/MouseBiteLabs/Game-Boy-MBC3-Cartridge/assets/97127539/236683cc-6e3e-4744-83c8-658e7b0f7ec8)

All gerbers and source files can be found in this repo, as this project is fully open source. Technical documentation of the board can be found in the Technical folder.

## Disclaimer

I am not responsible for any damage you do to your self or your property. I do not guarantee design compatibility. You may encounter issues with certain games! Attempt this project at your own risk.

## Board Characteristics and Order Information

The zipped folder contains all the gerber files for this board. The following options **must** be chosen when ordering boards for yourself.

- Thickness: 0.8mm
- Surface Finish: ENIG
- Gold Fingers: Yes, 30° chamfer

You can use the zipped folder at any board fabricator you like. You may also buy the board from PCBWay using this link (disclosure: I receive 10% of the sale value to go twoards future PCB orders of my own):

[add link]

<a href="https://oshpark.com/shared_projects/Uqmcescj">The board is also listed on OSH Park as well.</a> **Be sure to get them in 0.8mm thickness if you order from here.**

## Board Configurations

The board comes with five sets of jumper pads for solder bridges. SJ3, SJ5, and SJ6 require you to solder bridge the middle pad either to the left or right pads. SJ1 and SJ4 are configured by either leaving them alone or bridging them with solder. Here are the situations where you need to add solder bridges.

### Making Games Without SRAM or Battery (SJ1 and SJ4)

Solder bridge SJ1 and SJ4 if your game does not use any SRAM or battery.

### MBC3 Type Select (SJ3)

The MBC3 chip you use from the donor cartridge can be one of a few different types:

- If your MBC3 doesn't have a revision letter on it (it just says "MBC3" on it) then bridge the middle pad of SJ3 to the left. (You will also need to populate Q1!)
- If your MBC3 *does* have a revision letter, such as MBC3A or MBC3B, then bridge the middle pad of SJ3 to the right.

### SRAM Size Selection (SJ5 and SJ6, or SW1)

These jumpers are located underneath the MBC3 chip, and labeled "64K" and "256K". Solder them to configure the amount of RAM your cart uses. You must configure these pads for every game you make, unless you do not need RAM. <a href="https://catskull.net/gb-rom-database/">You can find a list of games here with their respective RAM sizes.</a>

- If the game you're making uses 256Kb of SRAM, then solder the middle pads of the two jumper sets to the right pads.
- If the game you're making uses 64Kb of SRAM, then solder the middle pads of the two jumper sets to the left pads.
- SJ5 and SJ6 must be soldered in the same direction.
- The footprint of these selection pads should allow for a DPDT switch, part number CAS-220A1, to be placed on these pads instead of having to bridge the pads with solder.

Note that you can make games that only require 64Kb of RAM and still use a 256Kb SRAM chip. You still need to configure the jumpers to the 64Kb setting, though.

## Bill of Materials (BOM)

Your parts list will vary depending on the game you are trying to make, and what chips you have for the battery management (if any). Note that C9 - C11 footprints are only included for edge cases that may require them; you can ignore them unless you run into issues.

Please carefully review the parts you need for the board you are trying to make. Do not add any parts to your build that don't appear in the column for the game you are making. This means you *cannot* populate every component on the board at the same time.

| Reference Designators | Value/Part Number              | Package          | Description        | No save carts | Save carts with U4 | Save carts without U4 | Source                                           |
| --------------------- | ------------------------------ | ---------------- | ------------------ | ------------- | ------------------ | --------------------- | ------------------------------------------------ |
| B1                    | CR2032, CR2025, CR2016         | CR2032           | Backup Battery     |               | x                  | x                     | [https://mou.sr/3SeAzfT](https://mou.sr/3SeAzfT) |
| C1                    | 0.1uF                          | 0603             | Capacitor (MLCC)   | x             | x                  | x                     | [https://mou.sr/3ENc15O](https://mou.sr/3ENc15O) |
| C2                    | 15pF                           | 0603             | Capacitor (MLCC)   |               | If using RTC       | If using RTC          | https://mou.sr/3PPorjO                           |
| C3                    | 15pF                           | 0603             | Capacitor (MLCC)   |               | If using RTC       | If using RTC          | https://mou.sr/3PPorjO                           |
| C4                    | 0.1uF                          | 0603             | Capacitor (MLCC)   |               | x                  |                       | [https://mou.sr/3ENc15O](https://mou.sr/3ENc15O) |
| C5                    | 0.1uF                          | 0603             | Capacitor (MLCC)   |               | x                  | x                     | [https://mou.sr/3ENc15O](https://mou.sr/3ENc15O) |
| C6                    | 10uF                           | 0603             | Capacitor (MLCC)   |               | x                  | x                     | [https://mou.sr/3mZtSkF](https://mou.sr/3mZtSkF) |
| C7                    | 0.1uF                          | 0603             | Capacitor (MLCC)   | x             | x                  | x                     | [https://mou.sr/3ENc15O](https://mou.sr/3ENc15O) |
| C8                    | 0.1uF                          | 0603             | Capacitor (MLCC)   |               |                    | x                     | [https://mou.sr/3ENc15O](https://mou.sr/3ENc15O) |
| C9                    | 0.001uF                        | 0603             | Capacitor (MLCC)   |               |                    |                       | [https://mou.sr/3PpAXoM](https://mou.sr/3PpAXoM) |
| C10                   | 0.001uF                        | 0603             | Capacitor (MLCC)   |               |                    |                       | [https://mou.sr/3PpAXoM](https://mou.sr/3PpAXoM) |
| C11                   | 0.001uF                        | 0603             | Capacitor (MLCC)   |               |                    |                       | [https://mou.sr/3PpAXoM](https://mou.sr/3PpAXoM) |
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

## Things to Remember

- The footprint for the EEPROM is specifically for 29F016 - it has 48 pins. However, 29F032 and 29F033 are only 40 pin devices. They still work fine on the board though - place them in the center of the footprint, and leave the outer two pins on each corner empty.
- The 29F016, 29F032, and 29F033 have been known to occasionally be defective upon arrival. They're usually only available from AliExpress.
- For battery management, use either U4 *or* U5 and U6 and supporting components. **Do not** use U4, U5, and U6 all on one board. They will interfere with each other.
- Kb is kilo**bits** and Mb is mega**bits**. Sometimes you will find game ROM and RAM sizes defined in terms of KB or kilo**bytes** and MB or mega**bytes**. You can convert Kb and Mb to KB and MB by dividing Kb or Mb by 8. For example, 256 Kb = 32 KB.
- You only need to provide ROM and RAM chips that have at least *or greater* the size of the game you are trying to make. That means you can use a 256Kb SRAM chip for a game that only requires 64Kb!

## Revision History

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
- <a href="https://www.alldatasheet.com/datasheet-pdf/pdf/99104/MITSUBISHI/MM1026.html">System Reset IC Datasheet</a>
- <a href="https://www.ti.com/lit/ds/symlink/tps3613-01.pdf?HQS=dis-mous-null-mousermode-dsf-pf-null-wwe&ts=1698238885366&ref_url=https%253A%252F%252Feu.mouser.com%252F">TPS3613 Datasheet</a>
- Board outline from <a href="https://tinkerer.us/projects/homebrew-gameboy-cartridge.html">Dillon Nichols's Homebrew Gameboy Cartridge project</a>
- Thank you to <a href="https://github.com/Gekkio">gekkio</a> for their deep Game Boy knowledge resources, and for collaboration in demystifying some of the design choices on Game Boy cartridges
- Thanks to the awesome members of the <a href="https://moddedgameboy.club/">Modded Gameboy Club</a> for their feedback and support during the entire project development

## License

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/80x15.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>. You are able to copy and redistribute the material in any medium or format, as well as remix, transform, or build upon the material for any purpose (even commercial) - but you **must** give appropriate credit, provide a link to the license, and indicate if any changes were made.

©MouseBiteLabs 2023
