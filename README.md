# Antminer S9 Control Board HAT
## Disclaimer

Following the global mining boom, large numbers of used Antminer S9 control boards have become widely available on secondary markets for as little as $5–30.
These boards, originally built to control cryptocurrency mining equipment, are based on the well-documented Xilinx Zynq-7010 (XC7Z010) SoC and include:

 - Two Arm® Cortex®-A9 processor cores

 - Gigabit Ethernet

 - 512 MB or 1 GB DDR3 RAM

 - 256 Mb SLC NAND flash

 - MicroSD card

 - Exposed PS–PL interface pins, some of which are connected to various peripherals
 
 - Supports Linux and FreeRTOS

Because of their solid hardware design and abundance, these boards make excellent low-cost development platforms for embedded Linux, RTOS, FPGA, or hardware-software co-design experiments — especially when combined with this HAT expansion board.

## Overview

This project is a KiCad-based expansion board (HAT) designed for the Antminer S9 Control Board built around the Xilinx Zynq-7010 (XC7Z010) SoC.
The board extends the original functionality of the control board by adding:

PS-side ULPI USB controller — enabling standard USB host/device connectivity via the Zynq Processing System.

HDMI output interface — connected directly to the Zynq PL (Programmable Logic) fabric for video output or diagnostics.

Breakout header exposing all remaining available PL I/O pins of the Zynq device (except 8 pins used for HDMI).

![3d Top wiew of the board](https://github.com/Kcctech-git/Antimner_S9_HatBoard/blob/main/Images/Top_view.png)

![3d Bottom wiew of the board](https://github.com/Kcctech-git/Antimner_S9_HatBoard/blob/main/Images/Bottom_view.png)

The goal of this project is to repurpose the Antminer S9 control board into a versatile FPGA development platform, enabling experimentation with Zynq PS/PL subsystems, video output, and general FPGA prototyping.

## Features

 - Compatible with Antminer S9 Zynq-7010 control boards

 - Adds USB ULPI PHY connected to the Zynq PS

 - Adds HDMI TX output using 8 PL pins for TMDS signaling

 - Breaks out all remaining PL I/O to 2.54 mm header

 - Designed in KiCad 9.0 (schematic + PCB files included)

 - Fits within the mechanical envelope of the S9 control board

### This HAT board enables multiple new use cases for the Antminer S9 Control Board:

 - Use as a Zynq FPGA development board with easy access to PL I/O

 - Implement custom video output via HDMI for FPGA or Linux framebuffer

 - Utilize USB host/device support through the PS-side ULPI PHY

 - Experiment with bare-metal or Linux projects using the onboard DDR, flash, and Ethernet

 - Perform reverse engineering, diagnostics, or educational FPGA projects on low-cost S9 boards

## Purpose of the Design

The main goal of this HAT board was to enable the built-in USB 2.0 controller inside the Zynq-7010 Processing System (PS).
Although the Zynq PS includes a native ULPI interface for USB, its signals on the Antminer S9 control board are routed to scattered locations and are not interconnected in a usable way.

![ULPI signals on Antminer S9 board](https://github.com/Kcctech-git/Antimner_S9_HatBoard/blob/main/Images/ULPI-Conn.jpg)

This board collects all required ULPI pins from different areas of the control board, aligns their routing lengths, and connects them to a USB PHY (USB3315C).
Because the parallel ULPI bus operates at approximately 60 MHz, and the trace length on the HAT is around 16 cm — close to the reliable signaling limit — special care was taken to maintain consistent line length and impedance.

To minimize signal reflections, 30 Ω series resistors were added on all ULPI data lines.
Testing confirmed that the USB 2.0 interface operates reliably and stably: files larger than 100 MB can be read and written from a USB flash drive without errors.

![ULPI signals routing](https://github.com/Kcctech-git/Antimner_S9_HatBoard/blob/main/Images/ULPI_Signals_routing.png)

## Hardware Modifications (Required)

To enable the PS-side ULPI USB interface, a few small modifications are required on the Antminer S9 Zynq-7010 Control Board.
These steps free PS MIO pins previously used by on-board peripherals and provide mechanical clearance for the HAT board.

### Steps

1. Remove Q2 and Q3
These transistors are connected to MIO lines needed for ULPI.
Removing them frees access to: PS_MIO37 and PS_MIO38

2. Wire PS_MIO37 and PS_MIO38 to J11
Solder 60 mm insulated wires from the Q2/Q3 gate pads to PS_MIO37, PS_MIO38

![Connection of PS_MIO37 and PS_MIO38 to J11](https://github.com/Kcctech-git/Antimner_S9_HatBoard/blob/main/Images/Connect_MIO37_38.jpg)

4. Wire PS_MIO39 to J11
Solder a 102 mm insulated wire from the resistor pad (previously tied to control logic) to PS_MIO39

![Connection of PS_MIO39 to J11](https://github.com/Kcctech-git/Antimner_S9_HatBoard/blob/main/Images/Connect_MIO39.jpg)

6. Remove capacitors for clearance
Desolder the following mechanically blocking capacitors to allow the HAT board to seat properly C195, C197, C229

7. Connect auxiliary signals to J11 (Aux MIO Connector on HAT)
8. Hook up the freed MIO lines and power signals to the J11 header on the HAT board.
See pinout table below.

9. Provide +12 V to J12 (12 V Pwr on HAT)
10. Connect the +12 V rail from the control board to the J12 input on the HAT.
The HAT generates 5 V and 1.8 V locally for its subsystems.

### J11 (Aux MIO Connector) Pinout
|  Pin	 |  Signal   |	Direction	Notes                                        |
| ----- | --------  | ------------------------------------------------------ |
| 1	    | USB_VDD_IO| (2.5 V)	Power	I/O voltage reference for Zynq PS Bank 1 |
| 2	    | RESET     |	Input	Optional; not used in the reference build        |
| 3     |	PS_MIO39  |	I/O	ULPI signal                                        |
| 4	    | PS_MIO38  |	I/O	ULPI signal                                        |
| 5     |	PS_MIO37  |	I/O	ULPI signal                                        |

### Note: Verify pin orientation according to the silkscreen and schematic before soldering.
Keep wires short (≤ 100 mm) and add strain relief where possible.

### Result:
After completing these modifications, the HAT board’s USB3315C ULPI controller is correctly connected to the Zynq PS USB interface through PS_MIO37–39, with the proper 2.5 V I/O reference and power supplied via J12.

![Assembled view of hat board](https://github.com/Kcctech-git/Antimner_S9_HatBoard/blob/main/Images/Assembled_view.jpg)

## Bill of Materials (BOM)

| Ref                    | Qty | Value            | Description / Notes                  | Footprint / Link                                                                                                            |
| ---------------------- | --- | ---------------- | ------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| **USB & Power**        |     |                  |                                      |                                                                                                                             |
| IC1                    | 1   | USB3315C-CP-TR   | ULPI USB PHY                         | [Microchip Datasheet](http://ww1.microchip.com/downloads/en/DeviceDoc/3315.pdf)                                             |
| IC2                    | 1   | LM3525M-H        | USB Power Switch                     | [TI Datasheet](https://www.ti.com/product/LM3525)                                                                           |
| J14                    | 1   | USB-C Receptacle | USB2.0 16-pin                        | [USB Type-C Spec](https://www.usb.org/sites/default/files/documents/usb_type-c.zip)                                         |
| J15                    | 1   | 48204-0001       | IO Connector (Molex type)            | [Molex 48204-0001](http://www.molex.com/webdocs/datasheets/pdf/en-us/0482040001_IO_CONNECTORS.pdf)                          |
| Y1                     | 1   | 24 MHz           | ULPI Clock Oscillator                | [Abracon ASV](http://www.abracon.com/Oscillators/ASV.pdf)                                                                   |
| **HDMI Interface**     |     |                  |                                      |                                                                                                                             |
| J13                    | 1   | HDMI Type-A      | Output Connector                     | [Molex 208658-1001](https://www.molex.com/en-us/products/part-detail/2086581001)                                            |
| D1, D6, D7             | 3   | CDDFN10-0524P    | HDMI ESD Protection                  | [Bourns CDDFN10-0524P](https://www.bourns.com/docs/Product-Datasheets/CDDFN10-0524P.pdf)                                    |
| **Voltage Regulation** |     |                  |                                      |                                                                                                                             |
| U1                     | 1   | L7805            | 5 V Linear Regulator                 | [ST L7805](https://www.st.com/resource/en/datasheet/l78.pdf)      |
| U2                     | 1   | LM1117DT-1.8     | 1.8 V Regulator                      | [TI LM1117](http://www.ti.com/lit/ds/symlink/lm1117.pdf)                                                                    |
| **Connectors**         |     |                  |                                      |                                                                                                                             |
| J1–J9                  | 9   | Conn_02x09       | PL / GPIO Headers (2 mm pitch)       | —                                                                                                                           |
| J11                    | 1   | Aux_mio_conn     | 1×5 Header 2.54 mm                   | —                                                                                                                           |
| J12                    | 1   | 12 V Pwr         | 2×2 Socket 2.54 mm                   | —                                                                                                                           |
| J16                    | 1   | PL_conn          | 1×37 Header 2.54 mm                  | —                                                                                                                           |
| **Passive Components** |     |                  |                                      |                                                                                                                             |
| C1                     | 1   | 47 µF 25 V       | Bulk decoupling                      | 1206                                                                                                                        |
| C2                     | 1   | 100 µF 10 V      | Bulk decoupling                      | 1206                                                                                                                        |
| C3                     | 1   | 100 nF           | Decoupling                           | 1206                                                                                                                        |
| C4                     | 1   | 1 nF             | Filter                               | 1206                                                                                                                        |
| C5, C6, C13, C23, C25  | 5   | 0.1 µF           | Bypass capacitors                    | 0805                                                                                                                        |
| C7                     | 1   | 100 µF           | Bulk capacitor                       | 1210                                                                                                                        |
| C8                     | 1   | 1 nF             | Filter                               | 1206                                                                                                                        |
| C14, C24               | 2   | 10 µF            | Local decoupling                     | 1206                                                                                                                        |
| R1                     | 1   | 1 MΩ             | Pull-down                            | 0805                                                                                                                        |
| R2                     | 1   | 820 Ω            | LED limiter                          | 0805                                                                                                                        |
| R3, R20                | 2   | 2 kΩ             | General pull-up                      | 0805                                                                                                                        |
| R4                     | 1   | 4.7 kΩ           | I²C pull-up                          | 0805                                                                                                                        |
| R5                     | 1   | 8.06 kΩ          | Divider                              | 0805                                                                                                                        |
| R6–R19                 | 11  | 30 Ω             | HDMI series termination              | 0805                                                                                                                        |
| R21                    | 1   | 1 MΩ             | General pull-down                    | 0805                                                                                                                        |
| RN1, RN2               | 2   | TC164-FR-0749R9L | 49.9 Ω resistor network (4×)         | [Yageo Datasheet](https://www.yageo.com/upload/media/product/productsearch/datasheet/rchip/PYu-YC_TC_group_51_RoHS_L_9.pdf) |
| D2, D8                 | 2   | LED              | Status indicators                    | Any 1206/1210 Leds                                                                                                                        |
| D4                     | 1   | 1N5822           | Schottky diode                       | [Vishay 1N5822](http://www.vishay.com/docs/88526/1n5820.pdf)                                                                |
| **Test Points**        |     |                  |                                      |                                                                                                                             |
| TP1–TP6                | 6   | —                | CEC, SCL, SDA, HPD, UTILITY, 5V_HDMI | TestPoint Ø1.5 mm                                                                                                           |


## Known Limitations

HDMI interface uses AC-coupled LVCMOS-TMDS signaling (non-standard but functional).

Voltage compatibility of some PL banks depends on existing S9 circuitry — verify I/O standards before connecting.

Power is drawn directly from the S9 board (3.3 V and 12 V rails).

## License

Licensed under CERN Open Hardware Licence v2 - Permissive (CERN-OHL-P)
SPDX-License-Identifier: CERN-OHL-P-2.0

Full license text:
https://ohwr.org/cern_ohl_p_v2.txt

## Credits

Designed by Kanstantsin Salamiyuk

Created to extend and repurpose the Antminer S9 Zynq-7010 control board as an open FPGA development platform.
KiCad design files and documentation are provided for educational and experimental use.

## Disclaimer

This project is not affiliated with or endorsed by Bitmain Technologies.
All hardware modifications and usage are performed at your own risk.
Improper connections may damage the control board or FPGA.

**Tags:** `FPGA` `Zynq-7010` `Antminer S9` `Embedded Linux` `ULPI` `HDMI` `Open Hardware`
