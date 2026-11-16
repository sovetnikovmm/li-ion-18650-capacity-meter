# Li-ion 18650 Capacity Meter

A single-channel capacity meter and charger for **18650 Li-ion cells**, built around the **STM32F051K8T6** microcontroller. The device measures capacity by discharging the cell through switched resistive loads and integrating the calculated current over time. A **16 × 2 I²C LCD** displays the battery voltage, discharge current, accumulated capacity, and elapsed time.

The project includes a custom PCB, an enclosure, an assembled prototype, and Russian engineering documentation. Firmware source files are not included.

## Block Diagram

[![Functional block diagram](hardware/images/block-diagram.png)](hardware/images/block-diagram.png)

The external 5 V supply powers the display, charger, and 3.3 V regulator. The microcontroller reads the battery voltage, switches the discharge loads, controls charging, and updates the display and indicators. A reset button and a separate mode button provide user control. The SWD connector provides access for programming and debugging.

## Schematic

[![Circuit schematic](hardware/images/schematic.png)](hardware/images/schematic.png)

The circuit contains the following functional sections:

- **Power input:** USB Type-C connector XS1 and AMS1117-3.3 regulator DA4 provide the 5 V and 3.3 V rails.
- **Charging and protection:** TP4056 DA3 charges the cell; DW01A DA1 and FS8205A DA2 form the battery protection circuit.
- **Discharge loads:** MOSFETs VT1 and VT3 switch the 10 Ω and 20 Ω power resistors R8 and R10. The STM32 measures cell voltage through divider R11/R12 and calculates the current from the active load resistance.
- **Control and indication:** STM32F051K8T6 DD1 controls the operating sequence. XP1 connects the LCD, XS3 provides SWD access, and SW2 connects the external mode button.

Capacity is accumulated as **Q = Σ(I × Δt) / 3600**, with current in mA and time in seconds, giving the result in mAh. Energy is accumulated from voltage and current over the same intervals.

## PCB

### 3D View

[![PCB 3D view](hardware/images/pcb-3d.png)](hardware/images/pcb-3d.png)

### Top Layer

[![PCB top layer](hardware/images/pcb-layout-top.png)](hardware/images/pcb-layout-top.png)

### Bottom Layer

[![PCB bottom layer](hardware/images/pcb-layout-bottom.png)](hardware/images/pcb-layout-bottom.png)

The PCB measures **85 × 70 mm** and uses **two copper layers** with ground pours. The board uses FR-4 with a **1.5 mm dielectric thickness** and **35 µm starting copper foil**. Most passive components use the 1206 package; the two discharge resistors, LEDs, battery holder, and headers use through-hole mounting. The battery holder is integrated into the board layout.

## Assembled Device

### PCB Prototype

[![Assembled PCB with an 18650 cell](hardware/images/pcb-real.jpg)](hardware/images/pcb-real.jpg)

### Enclosure Design

[![Device enclosure 3D view](hardware/images/device-3d.jpg)](hardware/images/device-3d.jpg)

The enclosure has a base and removable lid, with openings for the battery, LCD, indicators, USB connector, and control button. The mechanical design specifies **FDM printing in ABS** and overall dimensions of **104.68 × 82 × 35.89 mm**. The PCB and display are secured with M3 fasteners and threaded inserts.

### Built Device

[![Assembled device during charging](hardware/images/device-real.jpg)](hardware/images/device-real.jpg)

The photograph shows the assembled unit in charging mode, with the cell voltage and elapsed time on the LCD.

### Operation

1. Insert the 18650 cell with the correct polarity and connect a **5 V USB supply**.
2. Use the external mode button connected to SW2 to select measurement or charging.
3. In measurement mode, both load branches are initially enabled. At **3.30 V**, the 10 Ω branch is disconnected and discharge continues through the 20 Ω branch.
4. Measurement ends after three consecutive voltage readings at or below **3.0 V**. Both loads are disconnected, and the final screens show capacity, elapsed time, average current, and energy.
5. In charging mode, the discharge loads remain off and TP4056 charges the cell using its CC/CV sequence. The yellow indicator HL1 shows charging; the green indicator HL2 shows charge completion. Red indicator HL3 identifies measurement mode.

### Prototype Results

Prototype testing produced the following results for one test cell labelled **800 mAh**:

| Parameter | Recorded result |
| --- | ---: |
| Measured capacity | 717.8 mAh |
| Discharge duration | 1 h 21 min 16 s |
| Average discharge current | 529 mA |
| Measured energy | 2662.7 mWh |
| End-of-discharge voltage shown by the device | 3.01 V |
| End-of-charge voltage | 4.19 V on the device / 4.20 V on the multimeter |
| Charging duration | 1 h 10 min |

These are results for the tested sample. The difference from the cell's labelled capacity is not a calibration measurement of the instrument. A voltage-reading offset was observed during discharge, which affects calculated current and capacity.

## Bill of Materials

The table reproduces the project CSV BOM; unit symbols in the value column are formatted for readability.

| Designators | Qty | Value / Part | Description | Manufacturer | Part Number | Package |
| --- | ---: | --- | --- | --- | --- | --- |
| C1, C2, C6, C7, C8, C9 | 6 | 0.1 µF | Multilayer ceramic capacitor, 10% tolerance | Murata | GRM31C5C2A104JA01L | 1206 |
| C3 | 1 | 10 µF | Multilayer ceramic capacitor, 10% tolerance | Murata | GRM31CR61A106KA01L | 1206 |
| C4, C5 | 2 | 10 µF | Polarized electrolytic capacitor, 10 µF, 16 V, 20% tolerance | JB Capacitors | JCK1C100M040054 | ECAP SMD |
| DA1 | 1 | DW01A | Single-cell Li-ion battery protection controller | PUOLOP | DW01A | SOT-23-6 |
| DA2 | 1 | FS8205A | Dual N-channel power MOSFET | Fortune | FS8205A | TSSOP-8 |
| DA3 | 1 | TP4056 | Single-cell linear Li-ion battery charger with charge-status outputs | UMW | UMW TP4056 | ESOP-8 |
| DA4 | 1 | AMS1117-3.3 | Fixed-output linear voltage regulator, 3.3 V | UMW | AMS1117-3.3 | SOT-223 |
| DD1 | 1 | STM32F051K8T6 | 32-bit Arm Cortex-M0 microcontroller, 48 MHz, 64 KB Flash | STMicroelectronics | STM32F051K8T6 | LQFP-32 |
| HL1 | 1 | GNL-3014YD | Yellow LED 3mm | G-nor Optoelectronics | GNL-3014YD | THT |
| HL2 | 1 | GNL-3014GD | Green LED 3mm | G-nor Optoelectronics | GNL-3014GD | THT |
| HL3 | 1 | GNL-3014HD | Red LED 3mm | G-nor Optoelectronics | GNL-3014HD | THT |
| R1 | 1 | 1 kΩ | Fixed resistor 0.25W | Yageo | RC1206FR-071KL | 1206 |
| R2, R3 | 2 | 200 Ω | Fixed resistor 0.25W | Yageo | RC1206FR-07200RL | 1206 |
| R4, R5 | 2 | 5.1 kΩ | Fixed resistor 0.25W | Yageo | RC1206JR-075K1L | 1206 |
| R6, R17, R18, R19 | 4 | 100 Ω | Fixed resistor 0.25W | Yageo | RC1206JR-07100RL | 1206 |
| R7 | 1 | 1.2 kΩ | Fixed resistor 0.25W | Yageo | RC1206FR-071K2L | 1206 |
| R8 | 1 | 10 Ω | Fixed resistor 2W | Yageo | MO-2W-10RJ | C2-23 |
| R9 | 1 | 510 Ω | Fixed resistor 0.25W | Yageo | RC1206FR-07510RL | 1206 |
| R10 | 1 | 20 Ω | Fixed resistor 2W | Yageo | MO-2W-20RJ | C2-23 |
| R11, R12, R13, R14, R15, R16 | 6 | 10 kΩ | Fixed resistor 0.25W | Yageo | RC1206FR-0710KL | 1206 |
| R20, R21 | 2 | 4.7 kΩ | Fixed resistor 0.25W | Yageo | RC1206FR-074K7L | 1206 |
| SW1 | 1 | IT-1181A | Two-terminal switch internal | Ruichi | IT-1181A W=0.6mm [6x3x2.5] | SMD |
| SW2 | 1 | PLS-2R | Two-terminal switch external | Connfly Electronics | DS1022-1*2RDF11-B | THT |
| VD1 | 1 | BZT52C3V3S | Zener diode, 3.3 V, 0.2 W | Hottech | BZT52C3V3S | SOT-323 |
| VT1, VT3 | 2 | IRLML2502 | N-channel power MOSFET, 20 V | Hottech | IRLML2502 | SOT-23 |
| VT2 | 1 | IRLML6402 | P-channel power MOSFET, -20 V | Huashao | IRLML6402 | SOT-23 |
| XP1 | 1 | B4B-XH-A | 4-position plug connector | JST | B4B-XH-A | THT |
| XS1 | 1 | USB-C-6Pin | USB Type-C power receptacle, 6 contacts | Ruichi | USB3.1 TYPE-C 6PF-027 | SMD |
| XS2 | 1 | Battery 18650 | Single-cell 18650 battery holder | KLS Electronics | L-KLS5-18650-006-D | THT |
| XS3 | 1 | PLS-4 | 4-position socket connector | Connfly Electronics | DS1021-1*4SF11-B | THT |

[CSV BOM](docs/li-ion-18650-capacity-meter.csv) · [Altium BOM document](hardware/altium/li-ion-18650-capacity-meter.BomDoc)

The complete device additionally uses an **LCD1602 display with an I²C interface**, an external **PBS-33B-B button**, the printed enclosure, wiring, LED spacers, and M3 fasteners. These items are separate from the PCB BOM.

## Additional Notes

- Discharge current is calculated from measured voltage and nominal load resistance. Resistor tolerance and voltage calibration affect the capacity result.
- The 10 Ω and 20 Ω loads are rated at 2 W each and dissipate heat during measurement. The enclosure includes the battery opening shown in the mechanical design.
- USB Type-C is used for power input. The display and controls allow operation without a computer.

## Documentation (Russian)

- [Схема электрическая структурная (Э1)](docs/original-ru/li-ion-18650-capacity-meter_E1_block-diagram.pdf)
- [Схема электрическая принципиальная (Э3)](docs/original-ru/li-ion-18650-capacity-meter_E3_schematic.PDF)
- [Перечень элементов (ПЭ3)](docs/original-ru/li-ion-18650-capacity-meter_PE3_part-list.pdf)
- [Чертёж печатной платы](docs/original-ru/li-ion-18650-capacity-meter_PP_board-drawing.PDF)
- [Сборочный чертёж электронной ячейки](docs/original-ru/li-ion-18650-capacity-meter_SB_board-drawing-pcb.pdf)
- [Сборочный чертёж устройства](docs/original-ru/li-ion-18650-capacity-meter_SP_board-drawing-device.pdf)
- [Спецификация электронной ячейки](docs/original-ru/li-ion-18650-capacity-meter_SP_specification-pcb.pdf)
- [Спецификация устройства](docs/original-ru/li-ion-18650-capacity-meter_SP_specification-devicepdf.pdf)

## Design and Manufacturing Files

[Altium project](hardware/altium/li-ion-18650-capacity-meter.PrjPcb) · [Component libraries](hardware/altium/libs/) · [Gerber archive](fabrication/li-ion-18650-capacity-meter_gerber.zip) · [PCB STEP model](fabrication/li-ion-18650-capacity-meter.step) · [Device STEP model](fabrication/li-ion-18650-capacity-meter_device.stp)
