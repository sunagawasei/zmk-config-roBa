# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## プロジェクト概要

roBaは左右分離型のZMKキーボードファームウェア設定リポジトリ。Seeeduino XIAO BLE（nRF52840）をMCUとして使用し、右手側にPMW3610トラックボールセンサーを搭載。

## ビルド

GitHub Actionsで自動ビルド。ローカルビルドは不要（pushまたはPR作成時に自動実行）。

- ファームウェアビルド: `.github/workflows/build.yml` - push/PR/手動トリガー
- キーマップ図生成: `.github/workflows/draw.yml` - 手動トリガー（workflow_dispatch）

ビルド成果物はGitHub ActionsのArtifactsからダウンロード可能。

## アーキテクチャ

### ディレクトリ構成

```
config/
├── roBa.keymap     # キーマップ定義（レイヤー、コンボ、マクロ、ビヘイビア）
├── roBa.json       # ZMK Studio用物理レイアウト
└── west.yml        # Westマニフェスト（ZMK v0.3-branch + PMW3610ドライバー）

boards/shields/roBa/
├── roBa.dtsi       # 共通デバイスツリー（物理レイアウト、マトリクス、センサー）
├── roBa_L.overlay  # 左手側オーバーレイ（エンコーダー有効化）
├── roBa_R.overlay  # 右手側オーバーレイ（トラックボールSPI設定）
├── roBa_L.conf     # 左手側設定（ペリフェラル）
└── roBa_R.conf     # 右手側設定（セントラル、PMW3610、ZMK Studio）
```

### 左右分割構成

- **右手側（roBa_R）**: セントラル（親機）。トラックボール搭載、ZMK Studio対応
- **左手側（roBa_L）**: ペリフェラル（子機）。ロータリーエンコーダー搭載

### キーマップレイヤー

0. default_layer - QWERTY配列
1. FUNCTION - ファンクションキー
2. NUM - 数字・記号
3. ARROW - カーソル移動
4. MOUSE - マウスボタン（automouse-layer）
5. SCROLL - スクロールモード
6. layer_6 - Bluetooth設定、ブートローダー

### 外部依存

- ZMK本体: `zmkfirmware/zmk` (v0.3-branch)
- トラックボールドライバー: `kumamuk-git/zmk-pmw3610-driver`

## 編集時の注意

- キーマップ変更: `config/roBa.keymap`
- トラックボール設定変更: `boards/shields/roBa/roBa_R.conf`のCONFIG_PMW3610_*
- 物理配線変更: `boards/shields/roBa/roBa.dtsi`のkscan0とdefault_transform
