# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## プロジェクト概要

roBaは左右分離型のZMKキーボードファームウェア設定リポジトリ。Seeeduino XIAO BLE（nRF52840）をMCUとして使用し、右手側にPMW3610トラックボールセンサーを搭載。

- GitHub: `sunagawasei/zmk-config-roBa`

## ビルド・デプロイ

GitHub Actionsで自動ビルド。ローカルビルドは不要（pushまたはPR作成時に自動実行）。

- ファームウェアビルド: `.github/workflows/build.yml` - push/PR/手動トリガー
- キーマップ図生成: `.github/workflows/draw.yml` - **手動トリガーのみ**（workflow_dispatch）

ビルド成果物（`.uf2`）はGitHub ActionsのArtifactsからダウンロード。

### デプロイスキル

キーマップ変更後はスキルを使用：

```
/zmk-deploy
```

コミット→プッシュ→ビルド監視→ファームウェアダウンロード→書き込みを自動化。

### ビルドターゲット（build.yaml）

| shield | 備考 |
|--------|------|
| `roBa_R` | 右手側（studio-rpc-usb-uart snippetあり） |
| `roBa_L` | 左手側 |
| `settings_reset` | BLEペアリングリセット用 |

## アーキテクチャ

### ディレクトリ構成

```
config/
├── roBa.keymap     # キーマップ定義（レイヤー、コンボ、マクロ、ビヘイビア）
├── roBa.json       # ZMK Studio用物理レイアウト
└── west.yml        # Westマニフェスト（外部モジュール定義）

boards/shields/roBa/
├── roBa.dtsi       # 共通デバイスツリー（物理レイアウト、マトリクス、センサー）
├── roBa_L.overlay  # 左手側オーバーレイ（エンコーダー有効化、col-gpios）
├── roBa_R.overlay  # 右手側オーバーレイ（トラックボールSPI設定）
├── roBa_L.conf     # 左手側設定（ペリフェラル）
└── roBa_R.conf     # 右手側設定（セントラル、PMW3610、ZMK Studio）

firmware/           # ビルド済み.uf2ファイル（手動管理）
keymap-drawer/      # キーマップ可視化（YAML・SVG、draw.ymlで生成）
```

### 左右分割構成

- **右手側（roBa_R）**: セントラル（親機）。トラックボール搭載、ZMK Studio対応
- **左手側（roBa_L）**: ペリフェラル（子機）。ロータリーエンコーダー搭載

### キーマップレイヤー

| # | 名前 | 概要 |
|---|------|------|
| 0 | default_layer | QWERTY配列 |
| 1 | FUNCTION | ファンクションキー、スクリーンショット |
| 2 | NUM | 数字・記号 |
| 3 | ARROW | カーソル移動、Home/End |
| 4 | MOUSE | マウスボタン（automouse-layer自動遷移） |
| 5 | SCROLL | トラックボールスクロールモード |
| 6 | layer_6 | Bluetooth設定（BT_SEL 0-4、BT_CLR）、ブートローダー |

### 外部依存（west.yml）

- **ZMK本体**: `zmkfirmware/zmk` (v0.3-branch)
- **トラックボールドライバー**: `kumamuk-git/zmk-pmw3610-driver` (PMW3610センサー)
- **マウスキートグル**: `badjeff/zmk-behavior-mouse-key-toggle`

## 編集時の注意

| 変更内容 | 対象ファイル |
|----------|-------------|
| キーマップ変更 | `config/roBa.keymap` |
| トラックボール感度・動作 | `boards/shields/roBa/roBa_R.conf`のCONFIG_PMW3610_* |
| 物理配線変更 | `boards/shields/roBa/roBa.dtsi`のkscan0とdefault_transform |
| 外部モジュール追加 | `config/west.yml` |
