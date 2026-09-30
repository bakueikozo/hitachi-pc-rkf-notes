# PC-RKF ↔ dehumidifier protocol — WIP notes

Status: **partially decoded** (2026-09-30).  
Hardware under test: wall remocon **PC-RKF** + dehumidifier **RK-NP12PV2** (reheat-only compact floor unit).

Capture method: ZeroPlus logic analyzer @ 1 MHz on MM1192 MCU-side pins:

| LA | MM1192 pin | Function |
|----|------------|----------|
| A0 | 1 | Reception **DATA OUT** (to MCU) |
| A1 | 6 | **DATA IN** (from MCU) |

---

## 1. Physical layer

| Item | Finding |
|------|---------|
| Medium | 2-wire remocon bus (REMOCON A/B → unit TB2) |
| PHY IC | Mitsumi **MM1192** (HBS / AMI) |
| Bit rate | ~**9600 bps** (bit cell ≈ **104 µs**) |
| Line code | AMI-style: **pulse present = 0**, absence = 1 |
| Framing | UART-like over AMI: start `0`, 8 data **LSB first**, stop `1` (`0x00` often has stop quirks) |
| Inter-byte gap | Often ~1.25 ms between bytes in a burst |

**Not** Hitachi UART **H-Link** (CN7, 9600 8O1, ASCII `MT`/`ST`). Same brand, different PHY and frame format. Public H-Link code (e.g. esphome-hlink-ac) does **not** apply to A/B.

Closest public cousin at PHY only: Japanese Home Bus / Daikin P1P2-style AMI (MM1192 / MAX22088). Application layer is Hitachi-proprietary.

---

## 2. Exchange pattern (one button action)

Typical window (~100 ms):

```
Remocon TX  ████ 26-byte settings snapshot ████
            └─ almost identical echo on DATA OUT

Indoor unit      ░░░░ status / ACK (~45 bytes) ░░░░
(~45–50 ms)

Remocon                ▌ short trailing `21 03` (~100 ms)
```

- Master is the **remocon**: it pushes a full settings snapshot.
- Unit replies with ACK + echoed fields (mode/fan/RH).
- Looks more like event-driven snapshots than continuous register R/W (unlike H-Link).

---

## 3. Remocon → unit frame (26 bytes)

Skeleton:

```
21 00 1A 02  01 01 01 01 01  A1  [CMD] [FAN]  1A 10  [m0 m1 m2] [RH]  [P] 00 14 01 02 B0 00  [CS]
|---- header-ish ----|  |addr| |cmd| |fan|  |fixed| | mid  | |% | |pwr| |-- tail-ish --| |chk|
 idx: 0  1  2  3  4-------8   9   10    11   12 13  14 15 16  17  18  19-------------24   25
```

### 3.1 CMD (idx 10) — run / mode

| Value | Meaning (observed) | Unit ACK (after `A1`) |
|-------|--------------------|------------------------|
| `60` | Stop / settings while stopped | `40` |
| `C1` | Dehumidify run (reheat dehumidify) | `90` |
| `A1` | Fan-only run (送風) | `88` |

### 3.2 FAN (idx 11) — airflow

UI labels from the remocon panel:

| Value | Label |
|-------|--------|
| `08` | 弱風 (weak) |
| `04` | 強風 (strong) |
| `02` | 急風 (rapid / “kyūfū”) |

### 3.3 RH (idx 17) — target humidity

- One byte, **decimal percent** (not BCD).
- Confirmed pairs while stopped: `0x37` = 55%, `0x38` = 56%, `0x46` = 70%.
- Unit status frame echoes the same value after `… 01 [RH] …`.

### 3.4 Powerful dehumidify (idx 18)

| Value | Meaning |
|-------|---------|
| `40` | Normal (powerful off) |
| `90` | **Powerful on** (パワフル) |

Independently of FAN. Example (weak + powerful + 70%, running):

```
… A1 C1 08 1A 10 86 22 01 46 90 00 14 01 02 B0 00 12
         ^cmd ^weak          ^70% ^PWR
```

Unit ACK while powerful + weak showed fan nibble/echo as `24` (vs `08` without powerful).

### 3.5 Mid bytes (idx 14–16)

Varies across captures (`1A 64 C0`, `42 22 01`, `86 22 01`, `85 22 01`, …).  
Likely mode/flags or other settings — **not fully mapped yet**.

### 3.6 Checksum (idx 25)

Changes with payload. For the stopped weak 55↔56 pair, CS tracked RH by +1 (`57`/`58`).  
Full algebraic rule across all frame variants is **not locked** yet (XOR-of-all-bytes was constant `C1` only on some early frames).

---

## 4. Unit → remocon status (sketch)

Typical start:

```
12 00 18 01 01 01 01 01 01 A1 [ACK] [FAN'] 1A … 01 [RH] …
```

| ACK | Context |
|-----|---------|
| `40` | Stopped / config |
| `90` | Dehumidify running |
| `88` | Fan-only running |

`FAN'` usually echoes remocon FAN; with powerful + weak, `08` → `24` was observed.

---

## 5. Capture corpus (local work dirs)

Named folders under the operator’s capture tree (not all uploaded here):

| Folder / theme | What it established |
|----------------|---------------------|
| Idle + MM1192 pin map | A0=DO, A1=DI; AMI timing |
| Humidity 55/56 while stopped | RH = idx17 decimal % |
| Run / stop | CMD `C1` / `60`, ACK `90` / `40` |
| Weak / strong / rapid fan | FAN `08` / `04` / `02` |
| Fan-only mode | CMD `A1`, ACK `88` |
| Powerful on weak @ 70% | idx18 `40`→`90`; repeat capture byte-identical |

---

## 6. Comparison with known Hitachi stacks

| | This remocon bus | Public **H-Link** |
|--|------------------|-------------------|
| Connector | A/B 2-wire | Indoor CN7-style |
| PHY | HBS AMI (MM1192) | UART 9600 8O1 |
| Frames | Binary ~26 B snapshot | ASCII `MT`/`ST` |
| Access model | Push full state | Parameter address R/W |
| Reuse of esphome-hlink-ac | **No** | Yes (for H-Link units) |

RK-NP12PV2 also has **dry-contact** paths (e.g. remote start/stop on CN7 in the install manual) — separate from this bus.

---

## 7. Still open / next captures

Priority for closing the map:

1. Checksum formula across mid-byte variants  
2. Meaning of idx 14–16  
3. Idle / periodic traffic with no keypress  
4. Easy timer / schedule frames  
5. Inlet humidity display (long-press) — possible measured-RH in status  
6. Confirm remaining UI modes on this reheat-only SKU  

---

## 8. Safety / legal

Informal reverse engineering for interoperability and home automation.  
No warranty. Mains equipment — isolate properly; do not rely on this doc for life-safety control.
