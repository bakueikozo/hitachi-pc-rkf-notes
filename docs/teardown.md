# PC-RKF 分解メモ

写真は 2026-09-29 に詳細撮り直し。

## 写真

| ファイル | 内容 |
|----------|------|
| [01_rear_terminals_AB.png](photos/01_rear_terminals_AB.png) | 背面: REMOCON A/B、シール `PC-RKF H268 RKF-196060`、基板 `PC-ARF`、CN7、JP1/JP2 |
| [02_component_lcd_side.png](photos/02_component_lcd_side.png) | 液晶側: MM1192、ボタン BS1–BS9、TH1、LED GN/RD |
| [03_component_mcu_side.png](photos/03_component_mcu_side.png) | MCU側: **D78F1168A**、CN1（液晶FPC）、M51953B、MM1192、TH1 |
| [04_wiring_manual_floor_inverter.png](photos/04_wiring_manual_floor_inverter.png) | 据付シート: 小型床置インバータへの PC-RKF 接続（TB2 / CN6 / DSW2-2） |

## 表記（写真から）

| 項目 | 表記 |
|------|------|
| シール | PC-RKF / H268 / RKF-196060 |
| 基板シルク | PC-ARF、X59C、288H1 / 25 10 |
| スタンプ | CXZ11Z（背面） |
| 端子 | リモコン REMOCON A / B |
| バスIC | MITSUMI **MM1192**（HBS / AMI 物理層） |
| 主MCU | Renesas/NEC **D78F1168A**（78K0R）、ロット `2528AP` |
| リセットIC | Mitsubishi **M51953B** |
| 液晶 | 白ベゼルモジュール、FPC は **CN1** |
| 室温センサ | **TH1** ビーズサーミスタ |
| その他 | 背面 **CN7** 5pin、JP1/JP2、圧電ブザーパターン |

## 構成（推定）

```
本体 TB2 ----(2線)---- REMOCON A/B
                         |
                    結合／給電
                         |
                    MM1192（HBS / AMI PHY）
                         |
                    TTL Tx/Rx
                         |
                    D78F1168A
                      |- 液晶（CN1）
                      |- キー BS*
                      |- TH1
                      |- M51953B（リセット）
```

## 配線（据付シート・小型床置インバータ）

- リモコン A/B ↔ 室内機 **TB2** ↔ 基板 **PWB1** の **CN6**
- 延長ケーブル例: PRC-□K（ツイスト 0.75 mm²）
- 複数台: 最大10台、総配線長 200 m 未満
- PC-RKF 接続時は各台 **DSW2-2 = OFF**（電源オフで設定）
- リモコン線は電源線から 30 cm 以上離す（または鋼製電線管＋D種接地）

## スニフ／エミュレートの注意

1. まず A/B を観測（DCバイアス＋AMIパルス）
2. MCU GPIO を A/B に直結せず、**MM1192 の TTL 側**を優先
3. 78K0R 系プログラマは一般にフラッシュ**読出し**コマンドが無く、ファームダンプは期待しにくい
4. DIY PHY は **MAX22088** の方が MM1192 より入手しやすいことが多い → [`docs/datasheets/`](datasheets/)

## データシート

| 部品 | ファイル |
|------|----------|
| MM1192（搭載） | [MM1192_Mitsumi.pdf](datasheets/MM1192_Mitsumi.pdf) |
| MAX22088（HBS代替） | [MAX22088_AnalogDevices.pdf](datasheets/MAX22088_AnalogDevices.pdf) |
| XL1192 / XL1195 / XL1161 | [datasheets/README.md](datasheets/README.md) |

## 名称について

このリモコンバスは、エアコン等の UART「H-Link CN7」（9600 8O1、`MT`/`ST`）では**ない**。同系列ブランドでもインタフェースが異なる。
