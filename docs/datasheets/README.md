# Datasheets (HBS / AMI PHY)

Manufacturer documents redistributed here for offline reference. Copyright remains with the respective manufacturers. Prefer the official product page for the latest revision.

## On the PC-RKF board

| File | Part | Role |
|------|------|------|
| [MM1192_Mitsumi.pdf](MM1192_Mitsumi.pdf) | Mitsumi / MinebeaMitsumi **MM1192** | HBS-compatible driver/receiver (AMI). Used on PC-RKF |
| [MM1192_MinebeaMitsumi_product_sheet.pdf](MM1192_MinebeaMitsumi_product_sheet.pdf) | MM1192XFBE summary | Current product-sheet style PDF from MinebeaMitsumi site |

Sources:

- Full DS (historical Mitsumi layout): Octopart-hosted Mitsumi PDF  
- Product sheet: https://product.minebeamitsumi.com/en/product/category/ics/hbs/parts/MM1192.pdf  
- Product page: https://product.minebeamitsumi.com/en/product/category/ics/hbs/parts/MM1192.html  

## HBS-compatible alternatives (PHY class)

These claim **HBS / AMI twisted-pair** compatibility. They are **not pin-compatible** with MM1192. Use for DIY sniffers/emulators after verifying levels against a scoped PC-RKF bus. Upper-layer remocon protocol is still Hitachi-proprietary.

| File | Part | Notes | Availability |
|------|------|-------|----------------|
| [MAX22088_AnalogDevices.pdf](MAX22088_AnalogDevices.pdf) | Analog Devices **MAX22088** | Modern HBS transceiver, active inductor, 5 V LDO, DigiKey etc. | Good |
| — | Analog Devices **MAX22288** | Related HBS driver (power-sourcing oriented). DS not mirrored here (ADI download timed out) | See [product page](https://www.analog.com/en/products/max22288.html) |
| [XL1192_XLSEMI.pdf](XL1192_XLSEMI.pdf) | XLSEMI **XL1192** | Explicit MM1192-class HBS driver/receiver (SOP16) | China/broker |
| [XL1195_XLSEMI.pdf](XL1195_XLSEMI.pdf) | XLSEMI **XL1195** | HBS + dynamic impedance matching | China/broker |
| [XL1161_XLSEMI.pdf](XL1161_XLSEMI.pdf) | XLSEMI **XL1161** | HBS + 8–32 V → 5 V power management (bus-powered nodes) | China/broker |

Related Mitsumi family (not stored here; older HBS parts): **MM1007**, **MM1034** (with power supply). Search manufacturer / alldatasheet if needed.

## Recommendation for this project

1. **Reference / match OEM remocon:** stick to **MM1192** docs (what is on the board).  
2. **Build a bus adapter:** prefer **MAX22088** (easier purchasing) unless you can buy MM1192XFBE.  
3. Always validate DC bias and AMI amplitude on A/B before connecting a DIY node.
