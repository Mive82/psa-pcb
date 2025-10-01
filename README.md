# Important

**NOTE:** Unless you have access to a bunch of weird ICs used here, a way to solder 0201 and 0402 components,
and willpower to fix the defects listed below, don't bother assemblying the v1.0 version of the PCB.

# Peugeot Infotainment Project PCB

This is a repository for the PCB used in the second revision of the Peugeot Infotainment Project.
For the first revision, see the v0.1 branch.

It used <https://github.com/morcibacsi/PSAVanCanBridgeHW> as a base for the VAN communication hardware.

For more information, check out <https://github.com/Mive82/psa-docs>

## About the board

Unlike the previous v0.1 revision, on the v1.0 revision everything is contained on the board:
- Dual buck converter chips: One for the ESP and other ICs on the board (Battery power) and one for the Raspberry Pi and display (VAN+).
  - 3.3V LDO for the ESP32 after the 5V buck converter. Allows for very low voltage on the 12V input down to about 4 volts and the ESP will still work.
- ESP32-WROOM module. The USB to UART converter is not inclued, but the extra pins for resetting and flashing the board are routed to the connector.
- TSS463C VAN driver. Connected with a level shifter to the ESP
- MCP2551 CAN transciever. The RX pin is also connected to the ESP to allow receving in software.
- DS3231 RTC Chip for keeping the time. Connected to the Pi.

## Connections

The board has three JST GH connectors:
- Car power and data (JST GH 6 pin)
  - 12V battery power
  - 12V Switched (+VAN) power
  - VAN DATA and DATAB lines
- Display power output (JST GH 4 pin)
  - Outputs 5V for an external display
  - Also routes I2C pins from the Pi if needed
- ESP programming (JST GH 6 pin)
  - UART TX, RX, RTS and DTR pins as well as GND.

# Board Defects

Being the first proper revision, there are a lot of defects. Luckily they can all be solved.

## ESP flashing issues

The `GPIO12` pin is used for SPI communication with the TSS463C. It is also used as a strapping pin for determining the voltage used
to access the onboard flash. This pin is floating, so the value will not always be correct. This will result in regular booting
and flashing issues.

**SOLUTION**: Burn eFuses with `esptool.py` to permanently set the voltage used. On the ESP32-WROOM module we used, the voltage was 3.3V

## ESP first boot issues

When first powering the board on, the ESP will always boot into flash mode. This is because of a capacitor on the `GPIO0` pin that was not needed: `C6`.

**SOLUTION**: Remove the `C6` capacitor.

## VAN TX not working

The transistor switches used for putting the MCP2551 in a low-power state and resetting the TSS are not designed correctly.
They don't reset the TSS, but the MCP2551 is always put in a low power state which prevents it from sending any CAN/VAN data.

**SOLUTION**: Remove both transistor switches and surrounding hardware (`Q6`, `R21`, `R1`, `Q5` and `C1`). 
Pull pin `Rs` on the MCP2551 to GND with a 4.7k resistor. Note that, according the datasheet, this will increase power draw
by 9 or 10 mA.

## ESP32 VAN RX not working after VAN+ dissapears

This issue occured due to an oversight on level shifter pins. The VCC reference pin on one side is tied to the VAN+ regulator and will
dissapear when VAN+ dissapears.

**SOLUTION**: On chip U11 cut the trace connecting pin 11 (`VCC(B)`) and `+5V ignition`. then connect pin 11 to `+5V Car Battery`.
Best location would the one of the pads of `R21` which is be removed to fix the issue above this one.

### High deep sleep power consumption

The board pulls about 35mA on 12V during deep sleep. This increases the more voltage increases.
Not yet sure what is causing this, but removing the pull resistors from the `MODE` pins on the buck regulators reduces it to about 12mA.

**PARTIAL SOLUTION**: Remove `R110` and `R118`.