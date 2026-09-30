# データシート（HBS / AMI 物理層）

メーカー資料をオフライン参照用に保管しています。著作権は各メーカーに帰属します。最新版は公式製品ページを優先してください。

## PC-RKF 基板上

| ファイル | 部品 | 役割 |
|----------|------|------|
| [MM1192_Mitsumi.pdf](MM1192_Mitsumi.pdf) | Mitsumi / MinebeaMitsumi **MM1192** | HBS互換ドライバ／レシーバ（AMI）。PC-RKF 搭載 |
| [MM1192_MinebeaMitsumi_product_sheet.pdf](MM1192_MinebeaMitsumi_product_sheet.pdf) | MM1192XFBE 概要 | MinebeaMitsumi サイトの製品シート系 PDF |

入手元の例:

- フルデータシート（旧 Mitsumi 体裁）: Octopart 経由の Mitsumi PDF  
- 製品シート: https://product.minebeamitsumi.com/en/product/category/ics/hbs/parts/MM1192.pdf  
- 製品ページ: https://product.minebeamitsumi.com/en/product/category/ics/hbs/parts/MM1192.html  

## HBS互換の代替候補（物理層クラス）

いずれも **HBS / AMI・ツイストペア**互換を謳うもの。MM1192 と **ピン互換ではない**。  
DIY スニファ／エミュレータ用に、実機 A/B のレベルを確認したうえで検討する。上位のリモコンプロトコルは日立独自のまま。

| ファイル | 部品 | メモ | 入手性 |
|----------|------|------|--------|
| [MAX22088_AnalogDevices.pdf](MAX22088_AnalogDevices.pdf) | Analog Devices **MAX22088** | 現行HBSトランシーバ、アクティブインダクタ、5V LDO 等。DigiKey 等 | 比較的よい |
| — | Analog Devices **MAX22288** | 関連HBSドライバ（給電寄り）。本リポ未収録（ADI取得タイムアウト） | [製品ページ](https://www.analog.com/en/products/max22288.html) |
| [XL1192_XLSEMI.pdf](XL1192_XLSEMI.pdf) | XLSEMI **XL1192** | MM1192級のHBSドライバ／レシーバ（SOP16）明示 | 中国／商社経由 |
| [XL1195_XLSEMI.pdf](XL1195_XLSEMI.pdf) | XLSEMI **XL1195** | HBS＋動的インピーダンス整合 | 中国／商社経由 |
| [XL1161_XLSEMI.pdf](XL1161_XLSEMI.pdf) | XLSEMI **XL1161** | HBS＋8–32V→5V電源管理（バス給電ノード向け） | 中国／商社経由 |

関連の旧 Mitsumi 系（未収録）: **MM1007**、**MM1034**（電源付き）など。必要ならメーカー／alldatasheet を検索。

## 本プロジェクトでの推奨

1. **純正リモコンに合わせる参照:** **MM1192** 資料（基板搭載品）  
2. **バスアダプタ自作:** MM1192XFBE が手に入らなければ **MAX22088** を優先検討  
3. DIYノード接続前に、必ず A/B の DCバイアスと AMI振幅を実測で確認する
