

# INT Platform
## システム仕様
### Version 0.1 (Draft)

INT Platform は、現代のソフトウェア中心の制作ワークフローと、従来の LTC ベースの同期システムを接続するために設計された **ネットワーク型タイムコード配信システム**です。

本システムは以下を提供します。

- ネットワークタイムコードプロトコル **INTTC**
- タイムコード送信・受信・出力を行うデバイス役割
- 異なる制作環境間での柔軟な同期インフラ

---

# 1. 目的

INT Platform は以下の目的で設計されています。

- **ネットワークネイティブなタイムコード配信**
- **既存の LTC インフラとの互換性維持**
- 編集用途と同期用途の両方をサポート
- NLE やリアルタイムエンジンとの統合
- 将来の同期技術への拡張基盤

---

# 2. システム概要

INT Platform は主に 3 種類のデバイス役割で構成されます。

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

Sender の送信タイミングは INTTC プロトコル仕様によって定義されます。

参照:
INTTC Protocol v1.2 — Section 7 (Sender Transmission Model)

Sender は通常、プロジェクトのフレームレートとは独立した固定周期で
INTTC パケットを送信します。

参照実装のデフォルト設定:

- パケットレート: 60 Hz
- 送信間隔: 約 16.67 ms

各パケットは **Sender の論理タイムライン状態のスナップショット** を表します。

パケット送信スケジュールは「いつ送信するか」を決定するものであり、
タイムコードの値そのものを定義するものではありません。

Receiver はパケット到着時刻ではなく **パケット内容** を
タイミング状態の基準として扱う必要があります。

また Sender は、重要な状態変化が発生した場合に
即時パケットを送信することがあります。
```

---

## 3.2 Receiver

Receiver は INTTC パケットを受信し、タイムコードを解釈します。

主な役割：
代表的な Receiver タイミングパイプライン:

1. INTTC パケット受信
2. シーケンス番号検証 (SEQ)
3. ACTIVE によるソース解決
4. タイミングアンカー確立
5. ローカル Frame Clock による時間進行

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

INT Platform では時間を 3 層に分けて扱います。

```
Packet Time
Resolved Time
Output Time
```

この 3 層モデルにより、ネットワーク伝送・Receiver の解釈・実際の出力生成を
明確に分離します。

- Packet Time は Sender が INTTC で送信するタイムライン状態を表します。
- Resolved Time は Receiver が解釈した論理タイムコード状態です。
- Output Time は表示または信号生成に使用される時間値です。

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

INT Platform は 2 種類の動作ポリシーを定義します。

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


# 6.1 Input Modes

Bridge デバイスは、共有 BNC 入力コネクタを通じて複数の入力信号タイプをサポートする設計とすることができます。

この BNC 入力は、以下の **排他的入力モード**をサポートすることを想定しています。

```
LTC In
Sync In (将来実装)
```

同時に有効化できる入力モードは **1つのみ**とします。

Bridge v1 の実装では、まず LTC In のみをサポートし、Sync In は将来実装のために予約された機能として扱うことができます。

ハードウェア設計および基板レベルの設計では、同一 BNC 入力経路で将来 Sync In を実装できるよう、十分な拡張余地を残すことが推奨されます。

Sync In は target timecode の直接ソースではなく、出力生成のための **timing discipline / reference 信号**として使用されることが想定されています。

---

# 6.2 Status Indicators (LED)

Bridge デバイスは、外部ツールを使用せずに基本的な動作状態を確認できるよう、最小限のハードウェア状態表示を持つことが推奨されます。

推奨される最小構成は以下の 2 つの LED です。

```
Green LED
Red LED
```

典型的な意味付け:

Green LED（正常動作）:

- Boot / 初期化中: ゆっくり点滅
- パケット受信および出力生成がアクティブ: 点灯またはアクティビティ点滅

Red LED（警告または障害状態）:

- Stale パケット状態: 点滅
- Offline / Fault 状態: 点灯

具体的な LED パターンは実装によって異なっても構いませんが、可能な限り内部のクロックまたはネットワーク状態マシンを反映することが望ましいです。

---

# 6.3 Bridge Hardware Overview

一般的な Bridge 実装は、軽量なネットワーク受信処理と LTC 生成パイプラインを組み合わせた組み込みハードウェアとして構成されます。

参考実装例:

```
MCU: RP2040 (Pico2)
Network interface: W5500 Ethernet controller
Shared BNC input: LTC In / Sync In (排他モード)
Status indicators: Green LED, Red LED
```

ハードウェア実装は、将来の参照入力や同期機能の拡張に対応できるよう、柔軟性を保つ設計とすることが望まれます。

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

---

# 16. Timing Model Summary

INT Platform は時間処理を 3 つの概念レイヤーに分離しています。

```
Sender Timeline
      ↓
Packet Time (INTTC transport)
      ↓
Resolved Time (receiver interpretation)
      ↓
Output Time (signal generation)
```

このレイヤー構造により、Receiver はネットワークジッターを吸収しつつ
安定した出力タイミングを維持できます。

---

# 17. Network Behavior

INT Platform は低遅延のローカルネットワーク環境での動作を想定しています。

典型的な特性:

- UDP パケット伝送
- 小さなパケットサイズ
- 周期的な Sender 送信
- 軽微なパケットロスへの耐性

Receiver は以下の状況を処理できることが想定されています。

- パケット順序の入れ替わり
- 一時的なパケットロス
- パケット到着タイミングのばらつき

タイミング安定性はパケット到着間隔ではなく
Receiver のローカル Frame Clock によって維持されます。