

# INT Network Tools
## システム仕様
### Version 0.1 (Draft)

INT Network Tools は、現代のソフトウェア中心の制作ワークフローと、従来の LTC ベースの同期システムを接続するために設計された **ネットワーク型タイムコード配信システム**です。

本システムは以下を提供します。

- ネットワークタイムコードプロトコル **INTTC**
- タイムコード送信・受信・出力を行うデバイス役割
- 異なる制作環境間での柔軟な同期インフラ

---

# 1. 目的

INT Network Tools は以下の目的で設計されています。

- **ネットワークネイティブなタイムコード配信**
- **既存の LTC インフラとの互換性維持**
- 編集用途と同期用途の両方をサポート
- NLE やリアルタイムエンジンとの統合
- 将来の同期技術への拡張基盤

---

# 2. システム概要

INT Network Tools は主に 3 種類のデバイス役割で構成されます。

```
Sender
   ↓
INTTC Network
   ↓
Receiver / Bridge
```

例：

```
Resolve (Sender)
      │
      │ INTTC
      │
 ┌───────────────┬───────────────┐
 │               │               │
Receiver      Receiver        Bridge
(Display)     (Software)     (LTC Generator)
```

---

# 3. デバイス役割

## 3.1 Sender

Sender はタイムコード情報を含む INTTC パケットを生成します。

主な例：

- DaVinci Resolve プラグイン
- メディア再生システム
- タイムラインベースのソフトウェア

例：

```
INT TimeCode Tool
(DaVinci Resolve Workflow Integration Plugin)
```

### Sender 送信タイミング

Sender は、プロジェクトのフレームレートとは独立した固定レートで INTTC パケットを送信することが推奨されます。

デフォルトの Sender モード：

- パケットレート: 60 Hz
- 想定送信間隔: 約 16.67 ms (60 Hz)

各パケットは、Sender の送信タイミングにおける現在の論理タイムライン状態のスナップショットを表します。

パケット送信スケジュールは **いつパケットを送信するか** を決定するものであり、タイムコードの真の値そのものを定義するものではありません。

Receiver はパケットの到着時刻ではなく、**パケット内容**をタイミング状態の基準として扱う必要があります。

また Sender は、重要な状態変化が発生した場合に即時パケットを送信することがあります。例：

- 再生 / 停止の切り替え
- ソース選択の変更
- タイムコードの不連続
- フレームレート関連の設定変更
```

---

## 3.2 Receiver

Receiver は INTTC パケットを受信し、タイムコードを解釈します。

主な役割：

- パケット受信
- シーケンス番号追跡
- タイムコード解決
- 状態解釈

用途例：

- タイムコード表示
- モニタリングツール
- ソフトウェア統合

例：

```
SoftReceiver
```

---

## 3.3 Bridge

Bridge はネットワークタイムコードを **LTC 信号へ変換するデバイス**です。

主な役割：

- INTTC パケット受信
- タイムコード解決
- 出力タイミング維持
- LTC 信号生成

例：

```
INT TimeCode Bridge
RP2040 (Pico2) + W5500
```

---

# 4. タイミングアーキテクチャ

INT Network Tools では時間を 3 層に分けて扱います。

```
Packet Time
Resolved Time
Output Time
```

---

## 4.1 Packet Time

INTTC パケット内に含まれるタイムコード値。

特徴：

- Sender のタイムライン状態を反映
- ネットワーク遅延やジッターの影響を受ける
- パケット順序が入れ替わる可能性がある

---

## 4.2 Resolved Time

Receiver がパケット状態とソース選択を評価して決定する論理タイムコード。

処理例：

- SEQ による順序整理
- ACTIVE フィールドによるソース選択
- フリーズ検出
- パケット欠落検出

---

## 4.3 Output Time

実際の表示または信号出力に使用されるタイムコード。

以下の要因で Resolved Time と異なる場合があります。

- Holdover 処理
- Reference discipline
- Output Offset
- 再同期ロジック

---

# 5. Output Policy

INT Network Tools は 2 種類の動作ポリシーを定義します。

```
Loose
Tight
```

---

## 5.1 Loose

編集用途向けポリシー。

特徴：

- パケット駆動更新
- 小さなジッターを許容
- 高速再同期

用途例：

- クライアントモニター
- 編集レビュー
- ソフトウェア表示

---

## 5.2 Tight

同期精度が重要な環境向け。

特徴：

- 連続フレーム進行
- 安定したフレーム境界
- クロック制御出力

用途例：

- Unreal Engine
- LEDウォール
- 舞台照明
- メディアサーバー

---

# 6. Reference Source

Receiver や Bridge はローカルクロック安定化のため Reference Source を使用できます。

例：

```
Internal
Video Reference
PTP
GPS
LTC In
```

Reference Source は **Frame Clock の安定性を制御**します。

---

# 7. Frame Clock

Frame Clock は Receiver 内部で動作するフレーム進行エンジンです。

主な状態変数：

```
target_frame_time
current_frame_time
frame_rate
drop_frame
clock_state
reference_source
```

Frame Clock はパケット到着とは独立して動作します。

---

# 8. Frame Boundary Generator

Frame Boundary Generator はフレーム境界イベントを生成します。

各イベントで：

- フレーム時間更新
- 再同期処理
- Holdover 処理
- LTC エンコード開始

が実行されます。

---

# 9. Holdover Policy

パケット更新が失われた場合の動作を定義します。

```
None
Freeze
Run
```

### None

出力停止または無効化。

### Freeze

最後のタイムコードを保持。

### Run

ローカルクロックで継続。

---

# 10. Reacquire Behavior

パケット更新再開時の同期方法。

```
Snap
Smooth
```

### Snap

即座に目標時間へ同期。

### Smooth

徐々に同期。

---

# 11. Output Offset

出力タイミングに適用される符号付きオフセット。

用途：

- 下流機器の遅延補正
- システム間の同期調整

例：

```
+1 frame
-1 frame
+10 ms
-8 ms
```

---

# 12. LTC 出力パイプライン

Bridge の LTC 出力処理：

```
Resolved Time
   ↓
Output Policy Controller
   ↓
Timing Discipline
   ↓
Frame Boundary Generator
   ↓
Output Offset Apply
   ↓
LTC Frame Encoder
   ↓
Physical Signal Driver
```

Tight モードでは LTC 出力は Frame Clock により駆動されます。

---

# 13. Clock States

Receiver はクロック状態を保持することがあります。

```
FreeRun
Locked
Holdover
Converging
Fault
```

---

# 14. INTTC プロトコルとの関係

INTTC は **タイムコード配信のトランスポート層**です。

System Specification は以下を定義します。

- Receiver の時間解釈
- タイミングモデル
- 出力生成

```
INTTC Protocol
      ↓
Receiver Timing Model
      ↓
Output Generation
```

---

# Status

ドラフト仕様。

実装の進展に応じて変更される可能性があります。