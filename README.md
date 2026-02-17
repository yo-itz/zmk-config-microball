
  
# zmk-config-microball

`microball` 向けの ZMK 設定リポジトリです。  
`seeeduino_xiao_ble` を使った左右分割構成（`microball_L` / `microball_R`）を対象にしています。

## 対応構成

- Board: `seeeduino_xiao_ble`
- Shield: `microball_L`, `microball_R`
- 追加ビルド: `settings_reset`
- ZMK: `v0.2.1`（`config/west.yml`）

## ファイル構成（主要）

- `build.yaml`  
	GitHub Actions のビルドマトリクス
- `config/microball.keymap`  
	キーマップ・レイヤー・コンボ定義
- `config/boards/shields/Test/`  
	`microball` シールド定義（DTS/Kconfig）
- `config/west.yml`  
	ZMK と外部モジュール（`zmk-pmw3610-driver`）の依存定義

## レイヤー概要

- `DEFAULT` (0): 通常入力（QWERTY）
- `NUM` (1): 数字 + ナビゲーション
- `SYM` (2): 記号 + ファンクションキー
- `SYS` (3): Bluetooth 切替・システム
- `MOUSE` (4): オートマウス操作
- `SCROLL` (5): スクロール操作

主なコンボ（`config/microball.keymap`）:

- `Q + W` → `ESC`
- `S + D` → `TAB`
- `G + H` → `Caps Word`
- `Space + →NUM` → `変換`
- `Space + →SYM` → `無変換`

## ビルド（GitHub Actions）

1. このリポジトリを GitHub に push
2. Actions を実行（または push で自動実行）
3. Artifacts から `.uf2` を取得

`build.yaml` では以下をビルドします。

- `seeeduino_xiao_ble + microball_R`
- `seeeduino_xiao_ble + microball_L`
- `seeeduino_xiao_ble + settings_reset`

## 書き込み手順

1. 左右それぞれの MCU をブートローダーモードにする
	- **物理操作（確実）**: 対象側の `seeeduino_xiao_ble` のリセットボタンを素早く2回押す
	- **キーマップ操作**: `SYS` レイヤーから `BTLDR`（`&bootloader`）を実行
	- ブートローダーモードに入ると、PC側でUSBマスストレージ（UF2コピー先）として認識される
	- 左右は同時ではなく、**片側ずつ**ブートローダーに入れて書き込む
2. 対応する `.uf2` をドライブへコピー
	 - 左: `microball_L` 用
	 - 右: `microball_R` 用
3. 必要に応じて `settings_reset` を書き込んで設定を初期化

## ローカルビルド（任意）

ZMK の開発環境がある場合は、`west` でローカルビルド可能です。  
ただしこのリポジトリは GitHub Actions でのビルド運用を前提にしています。

## メモ

- 右手内側列の同時押しで `SYS` レイヤーにアクセス（BT切替）
- `SCROLL` レイヤーは `I` キー長押しで有効化


