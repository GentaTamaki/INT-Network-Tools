

# INTTC プロトコル
## Version 1.2

INTTC（INT TimeCode）は、ネットワーク上でタイムコードを配信するために設計された軽量な UDP ベースのプロトコルです。

本プロトコルは、現代のソフトウェアベースの制作環境と、従来の LTC（Linear Timecode）インフラを接続することを目的としています。

主な用途：

- NLE ソフトウェア統合
- バーチャルプロダクション
- 舞台システム
- 放送ワークフロー
- LTC ブリッジハードウェア

INTTC は **ネットワークネイティブなタイムコード伝送レイヤー**として機能します。

---

# 1. トランスポート

INTTC パケットは UDP を使用して送信されます。

デフォルトポート：

```
UDP 8001
```

本プロトコルは **低遅延のローカルネットワーク環境**での使用を想定しています。

一般的な送信方法：

- Broadcast
- Multicast
- Direct UDP

---

# 2. パケット構造

INTTC パケットは以下のフィールドで構成されます。

```
INTTC1
VER
SEQ
SRC_ID
STATE
SRC_TC
ALT_TC
ACTIVE
DISPLAY
OUTPUT
FPS
DF
```

---

# 3. フィールド定義

## VER

プロトコルバージョン識別子。

例：

```
1
```

---

## SEQ

単調増加するパケットシーケンス番号。

用途：

- パケット順序管理
- パケットロス検出
- フリーズ検出

例：

```
10521
```

---

## SRC_ID

Sender を識別するユニーク ID。

推奨形式：

```
6文字
[A-Z0-9]
```

例：

```
RSV001
VPST01
```

---

## STATE

Sender の動作状態。

値：

```
RUN
STOP
PAUSE
IDLE
```

Receiver はこの値を使用して送信側の状態を判断します。

---

## SRC_TC

主タイムコード値。

フォーマット：

```
HH:MM:SS:FF
```

例：

```
01:12:33:10
```

---

## ALT_TC

補助タイムコード。

セカンダリタイムコードが存在する場合に使用されます。

用途例：

- Source / Record タイムコード
- Capture / Playback タイムコード

---

## ACTIVE

現在使用されているタイムコードソース。

例：

```
SRC
ALT
LTCIN
```

---

## DISPLAY

Receiver に対する表示推奨。

例：

```
SRC
ALT
```

Receiver はこの値を無視することもできます。

---

## OUTPUT

Bridge デバイスに対する出力推奨。

例：

```
SRC
ALT
```

Bridge は LTC 出力に使用するタイムコードを選択できます。

---

## FPS

タイムコードのフレームレート。

例：

```
23.976
24
25
29.97
30
59.94
```

---

## DF

ドロップフレームフラグ。

値：

```
0 = Non Drop Frame
1 = Drop Frame
```

---

# 4. パケット例

例：

```
INTTC1
VER=1
SEQ=10452
SRC_ID=RSV001
STATE=RUN
SRC_TC=01:23:45:12
ALT_TC=01:23:45:12
ACTIVE=SRC
DISPLAY=SRC
OUTPUT=SRC
FPS=23.976
DF=0
```

---

# 5. Sender アナウンス

Sender はネットワーク上で自身を通知するためのアナウンスパケットを送信できます。

タイプ：

```
INTTC_SENDER
```

例フィールド：

```
SRC_ID
NAME
FPS
STATE
```

Receiver はこの情報を使用して利用可能な Sender を検出できます。

---

# 6. Bridge アナウンス

Bridge デバイスも自身の存在を通知できます。

タイプ：

```
INTTC_BRIDGE
```

例フィールド：

```
BRIDGE_ID
NAME
CAPABILITIES
```

CAPABILITIES 例：

```
LTC_OUT
AUDIO_LTC
REFERENCE_IN
```

---

# 7. Receiver の動作

Receiver は以下の処理ロジックを実装します。

1. SEQ によるパケット順序管理
2. パケットロス検出
3. ACTIVE フィールドによるソース選択
4. Resolved Time の生成
5. Output Policy に基づく出力生成

Receiver は以下のタイミングモデルを実装することがあります。

- Holdover
- Smooth Reacquire
- Reference Discipline

## 7.1 Sender 送信モデル

INTTC Sender は、プロジェクトのフレームレートとは独立した固定レートでパケットを送信することが推奨されます。

デフォルトの Sender モード：

- パケットレート: 60 Hz
- 想定送信間隔: 約 16.67 ms (60 Hz)

各パケットは、Sender の送信タイミングにおける論理タイミング状態のスナップショットを表します。これには以下の情報が含まれます。

- primary timecode
- alternate timecode
- sender state
- active source
- frame rate
- drop-frame flag

INTTC パケットは「1フレームごとに送信されるメッセージ」として定義されるものではなく、将来のフレームをスケジュールするための指示として解釈されるべきではありません。

Receiver はパケットの到着時刻ではなく、パケット内容をタイミング状態の基準として扱う必要があります。

また Sender は、重要な状態変化が発生した場合に追加で即時パケットを送信することがあります。例：

- 再生 / 停止の切り替え
- ソース選択の変更
- タイムコードの不連続（この場合 Sender は即時パケットを送信することが推奨されます）
- フレームレート関連の変更

---

# 8. 設計目標

INTTC プロトコルは以下を重視して設計されています。

1. **低遅延**
2. **実装のシンプルさ**
3. **既存タイムコードシステムとの互換性**
4. **将来拡張性**

---

# 9. 将来拡張

将来的に以下の拡張が可能です。

- PTP 同期
- GPS 同期
- マルチ Sender 制御
- 冗長化プロトコル
- メタデータチャネル

---

# 10. LTC との関係

INTTC は LTC を置き換えるものではありません。

INTTC は **ネットワークタイムコード配信レイヤー**として機能し、Bridge デバイスが LTC 信号へ変換します。

```
Software Sender
        ↓
      INTTC
        ↓
      Bridge
        ↓
        LTC
        ↓
Legacy Devices
```

---

# Status

ドラフト仕様。

実装の進展に伴い変更される可能性があります。