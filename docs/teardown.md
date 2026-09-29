# PC-RKF teardown notes

Photos retaken 2026-09-29 (higher detail).

## Photos

| File | Description |
|------|-------------|
| [01_rear_terminals_AB.png](photos/01_rear_terminals_AB.png) | Rear: REMOCON A/B, label `PC-RKF H268 RKF-196060`, PCB `PC-ARF`, CN7, JP1/JP2 |
| [02_component_lcd_side.png](photos/02_component_lcd_side.png) | Front with LCD: MM1192, buttons BS1–BS9, TH1, LEDs GN/RD |
| [03_component_mcu_side.png](photos/03_component_mcu_side.png) | MCU side: **D78F1168A**, CN1 (LCD FPC), M51953B, MM1192, TH1 |
| [04_wiring_manual_floor_inverter.png](photos/04_wiring_manual_floor_inverter.png) | Hitachi sheet: PC-RKF to small floor inverter units (TB2 / CN6 / DSW2-2) |

## Markings (from photos)

| Item | Text |
|------|------|
| Label | PC-RKF / H268 / RKF-196060 |
| PCB silk | PC-ARF, X59C, 288H1 / 25 10 |
| Stamp | CXZ11Z (rear) |
| Terminals | リモコン REMOCON A / B |
| Bus IC | MITSUMI **MM1192** (HBS / AMI PHY) |
| Main MCU | Renesas/NEC **D78F1168A** (78K0R), lot `2528AP` |
| Reset IC | Mitsubishi **M51953B** |
| LCD | Module in white bezel; FPC via **CN1** |
| Ambient sensor | **TH1** bead thermistor |
| Extra | **CN7** 5-pin (rear), JP1/JP2, piezo pattern |

## Architecture (inferred)

```
Indoor unit TB2 ----(2-wire)---- REMOCON A/B
                                   |
                              coupling / supply
                                   |
                              MM1192 (HBS / AMI PHY)
                                   |
                              TTL Tx/Rx
                                   |
                              D78F1168A
                                |- LCD (CN1)
                                |- keys BS*
                                |- TH1
                                |- M51953B (reset)
```

## Wiring (from Hitachi sheet, small floor inverter)

- Remocon A/B ↔ indoor **TB2** ↔ board **CN6** on **PWB1**
- Optional extension cable: PRC-□K (twisted pair 0.75 mm²)
- Multi-unit: up to 10 indoor units, total wiring &lt; 200 m
- **DSW2 pin 2 = OFF** on every unit when PC-RKF is connected (power off while setting)
- Keep remocon cable ≥ 30 cm from power lines (or steel conduit + D-class earth)

## Sniffing / emulation tips

1. Scope A/B first (DC bias + AMI pulses).
2. Prefer MM1192 TTL side over hard-wiring MCU GPIO to A/B.
3. 78K0R serial programmers generally have no flash **read** command; easy firmware dump is unlikely.
4. DIY PHY: **MAX22088** is usually easier to buy than MM1192.

## Note on naming

This remocon bus is **not** the UART “H-Link CN7” (9600 8O1, `MT`/`ST`) used by many Hitachi split ACs. Same brand family, different interface.
