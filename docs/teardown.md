# PC-RKF teardown notes

## Photos

| File | Description |
|------|-------------|
| [01_rear_terminals_AB.png](photos/01_rear_terminals_AB.png) | Rear: REMOCON A/B terminals, label, CN7 |
| [02_component_lcd_side.png](photos/02_component_lcd_side.png) | Component side (LCD): MM1192, buttons, TH1 |
| [03_component_mcu_side.png](photos/03_component_mcu_side.png) | Component side (MCU): LCD module, main MCU |

## Markings

| Item | Text |
|------|------|
| Label | PC-RKF H28B RKF-196060 |
| PCB | PC-ARF, 288H1 / 2510 |
| LCD module | TS-GG256160-01W |
| Terminals | REMOCON A / B |
| Bus IC | MITSUMI MM1192 |
| MCU | D78F1168A (78K0R) |
| Extra | CN1 (LCD FPC), CN7 (5-pin, likely diagnostic), JP1/JP2 |

## Architecture (inferred)

```
Indoor unit TB2 ----(2-wire)---- REMOCON A/B
                                   |
                              coupling / supply
                                   |
                              MM1192 (HBS / AMI PHY)
                                   |
                              TTL Tx/Rx to MCU
                                   |
                              D78F1168A
                                |- LCD
                                |- keys
                                |- TH1
```

## Sniffing / emulation tips

1. Capture on A/B with a scope first (DC bias + AMI pulses).
2. Prefer probing MM1192 TTL side over hard-wiring ESP32 to A/B.
3. Official flash readout on 78K0R is generally not available via serial programmer “read”; do not expect an easy firmware dump.
4. For a DIY transceiver, **MAX22088** is usually easier to source than MM1192.

## DIP note (dehumidifier body)

When connecting PC-RKF to RK-NP*PV2 series, set **DSW2-2 = OFF** on the indoor unit board (per Hitachi install manual).
