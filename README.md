# 日立 PC-RKF / HBS リモコン解析メモ

業務用・産業用除湿機向け壁リモコン **PC-RKF**（実機例: **RK-NP12PV2** 接続）の分解・ロジアナ解析メモです。  
物理層は Home Bus（HBS）／AMI 系です。

## 現状（2026-09-30）

**アプリケーション層は部分解読済み。**  
物理層を特定し、リモコン→本体の **26バイト**設定フレームについて、運転／停止・風量・湿度・パワフル・送風モードをマップ済み。チェックサム式と中間バイト（idx14–16）は未確定。

詳細: **[`docs/protocol-wip.md`](docs/protocol-wip.md)**

### プロトコル早見（MM1192 マイコン側 TTL）

| 項目 | 位置 | 既知の値 |
|------|------|----------|
| 運転コマンド | idx10 | `60` 停止／設定、`C1` 再熱除湿運転、`A1` 送風運転 |
| 風量 | idx11 | `08` **弱風**、`04` **強風**、`02` **急風** |
| 目標湿度 | idx17 | 十進 %（例: `0x37`=55%、`0x38`=56%、`0x46`=70%） |
| パワフル | idx18 | `40` オフ、`90` **パワフル**オン |
| 本体ACK | 応答フレーム | `40` 停止系／`90` 除湿運転／`88` 送風運転 |

ビット周期 ≈ **9600 AMI**（パルスあり＝0）。プローブは MM1192 **1番 DATA OUT** ＋ **6番 DATA IN**。

## ハードウェア要点

| 項目 | 内容 |
|------|------|
| 型式 | PC-RKF（シール例: `PC-RKF H268 RKF-196060`） |
| 基板シルク | `PC-ARF`（他の日立壁リモコンと共用の可能性） |
| 端子 | **リモコン REMOCON A / B**（2線） |
| バスIC | **MinebeaMitsumi MM1192**（HBS互換・AMI） |
| 主MCU | Renesas **D78F1168A**（78K0R） |
| 室温センサ | サーミスタ **TH1** |
| リセットIC | Mitsubishi **M51953B** |

このバスは、エアコン等で使われる UART 系 **H-Link（CN7・9600 8O1・`MT`/`ST`）とは別物**です。  
[esphome-hlink-ac](https://github.com/lumixen/esphome-hlink-ac) 等のコードは A/B には流用できません。

## 写真・分解

[`docs/photos/`](docs/photos/) および [`docs/teardown.md`](docs/teardown.md)

## 関連リンク

- [lumixen/esphome-hlink-ac](https://github.com/lumixen/esphome-hlink-ac) — 日立エアコン向け UART H-Link（物理層が異なる）
- [Analog Devices: Introduction to Home Bus](https://www.analog.com/en/resources/design-notes/introduction-to-home-bus.html)
- MM1192 製品ページ（MinebeaMitsumi）
- DIY向けHBS代替IC: **MAX22088**（MM1192より入手しやすいことが多い）

## データシート

[`docs/datasheets/`](docs/datasheets/) に以下を保管:

- 搭載 **MM1192** ＋ MinebeaMitsumi 製品シート  
- HBS互換候補: **MAX22088**、**XL1192**、**XL1195**、**XL1161**  

索引: [`docs/datasheets/README.md`](docs/datasheets/README.md)

## ライセンス

本リポジトリの文書・写真: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)  
無保証。感電・機器破損に注意し、安全を最優先してください。
