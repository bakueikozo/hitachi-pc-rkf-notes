# Hitachi PC-RKF / HBS remocon notes

Notes from reverse-engineering a Hitachi **PC-RKF** wall remocon (used with industrial dehumidifier **RK-NP12PV2** and related units). Shared for others working on Home Bus (HBS) / AMI remocon buses.

## Status

**Partial application decode (2026-09-30).**  
Physical layer identified; remocon→unit **26-byte** snapshot mapped for run/stop, fan, humidity, powerful, and fan-only mode. Checksum and mid-bytes still open.

Full write-up: **[`docs/protocol-wip.md`](docs/protocol-wip.md)**

### Protocol snapshot (MCU-side MM1192 TTL)

| Field | Index | Known values |
|-------|-------|----------------|
| CMD | 10 | `60` stop/settings, `C1` dehumidify run, `A1` fan-only |
| FAN | 11 | `08` 弱, `04` 強, `02` 急風 |
| RH | 17 | decimal % (`0x37`=55, `0x38`=56, `0x46`=70, …) |
| Powerful | 18 | `40` off, `90` on |
| Unit ACK | (status) | `40` / `90` / `88` for stop / dehum run / fan-only |

Bit timing ≈ **9600 AMI** (pulse = 0). Probed MM1192 pin1 DATA OUT + pin6 DATA IN.

## Key hardware findings

| Item | Value |
|------|--------|
| Remocon model | PC-RKF (label example: `PC-RKF H268 RKF-196060`) |
| PCB silk | `PC-ARF` (likely shared with other Hitachi wall remocons) |
| Bus terminals | **REMOCON A / B** (2-wire) |
| PHY transceiver | **MinebeaMitsumi MM1192** (HBS-compatible, AMI) |
| Main MCU | Renesas **D78F1168A** (78K0R), confirmed on photo |
| Ambient sensor | Thermistor TH1 on the remocon board |
| Reset IC | Mitsubishi **M51953B** |

This bus is **not** the UART “H-Link CN7” (9600 8O1, `MT`/`ST`) used by many Hitachi split ACs and by projects such as [lumixen/esphome-hlink-ac](https://github.com/lumixen/esphome-hlink-ac). Same brand, different physical interface.

## Photos / teardown

See [`docs/photos/`](docs/photos/) and [`docs/teardown.md`](docs/teardown.md).

## Related public projects

- [lumixen/esphome-hlink-ac](https://github.com/lumixen/esphome-hlink-ac) — UART H-Link for Hitachi AC (different PHY)
- [Analog Devices: Introduction to Home Bus](https://www.analog.com/en/resources/design-notes/introduction-to-home-bus.html)
- MM1192 product page (MinebeaMitsumi)
- Practical HBS alternative IC: **MAX22088** (easier to buy than MM1192)

## Datasheets

See [`docs/datasheets/`](docs/datasheets/) for:

- **MM1192** (on-board Mitsumi HBS transceiver) + MinebeaMitsumi product sheet  
- HBS-compatible alternatives: **MAX22088**, **XL1192**, **XL1195**, **XL1161**  

Index: [`docs/datasheets/README.md`](docs/datasheets/README.md)

## License

Documentation and photos in this repository: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).  
No warranty. Do not brick hardware; respect local electrical safety rules.
