# Hitachi PC-RKF / HBS remocon notes

Notes from reverse-engineering a Hitachi **PC-RKF** wall remocon (used with industrial dehumidifier **RK-NP12PV2** and related units). Shared for others working on Home Bus (HBS) / AMI remocon buses.

## Status

Work in progress. Physical layer identified; application protocol not fully decoded yet.

## Key findings

| Item | Value |
|------|--------|
| Remocon model | PC-RKF (label example: `PC-RKF H28B RKF-196060`) |
| PCB silk | `PC-ARF` (likely shared with other Hitachi wall remocons) |
| Bus terminals | **REMOCON A / B** (2-wire) |
| PHY transceiver | **MinebeaMitsumi MM1192** (HBS-compatible, AMI) |
| Main MCU | Renesas **D78F1168A** (78K0R), confirmed on photo |
| Ambient sensor | Thermistor TH1 on the remocon board |
| Reset IC | Mitsubishi **M51953B** |

This bus is **not** the UART “H-Link CN7” (9600 8O1, `MT`/`ST`) used by many Hitachi split ACs and by projects such as [lumixen/esphome-hlink-ac](https://github.com/lumixen/esphome-hlink-ac). Same brand, different physical interface.

## Photos

See [`docs/photos/`](docs/photos/) and [`docs/teardown.md`](docs/teardown.md).

## Related public projects

- [lumixen/esphome-hlink-ac](https://github.com/lumixen/esphome-hlink-ac) — UART H-Link for Hitachi AC (different PHY)
- [Analog Devices: Introduction to Home Bus](https://www.analog.com/en/resources/design-notes/introduction-to-home-bus.html)
- MM1192 product page (MinebeaMitsumi)
- Practical HBS alternative IC: **MAX22088** (easier to buy than MM1192)

## License

Documentation and photos in this repository: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).  
No warranty. Do not brick hardware; respect local electrical safety rules.
