

# INT Network Tools

**クリエイティブワークフロー向けネットワークタイムコード基盤**

INT Network Tools は、ネットワーク環境でタイムコードを送信・解釈・配信するためのシステムアーキテクチャです。  
従来の **LTC（Linear Timecode）ベースのワークフロー**との互換性を保ちながら、ソフトウェア中心の制作環境と既存のプロフェッショナル機器を接続することを目的としています。

このプロジェクトは、放送・ポストプロダクション・舞台・バーチャルプロダクションなどで使用されている **既存のタイミングインフラ**と、現代のソフトウェア制作環境の橋渡しを行うことを目標としています。

---

# ドキュメント

## Philosophy（設計思想）

- English: `docs/philosophy/INT_Philosophy.md`
- 日本語: `docs/philosophy/INT_Philosophy_JA.md`

## System Specification（システム仕様）

- English: `docs/system/INT_System_Spec_v0.1.md`
- 日本語: `docs/system/INT_System_Spec_v0.1_JA.md`

## INTTC Protocol（プロトコル仕様）

- English: `docs/protocol/INTTC_Protocol_v1.2.md`
- 日本語: `docs/protocol/INTTC_Protocol_v1.2_JA.md`

---

# プロジェクトの目的

INT Network Tools は以下を目標としています。

- **ネットワークネイティブなタイムコード配信**
- **既存の LTC ハードウェアとの互換性維持**
- **編集用途とフレーム同期用途の両方に対応**
- 将来の同期技術（PTP / GPS など）への拡張基盤

---

# 基本コンセプト

## ネットワークタイムコードレイヤー

INT Network Tools は既存の同期システムを置き換えるのではなく、  
**ネットワーク上にタイムコード配信レイヤーを追加する**ことを目的としています。

ソフトウェアシステムがタイムコードを生成し、ネットワーク経由で配信し、  
必要に応じて LTC 信号としてハードウェアへ出力することができます。

---

## Output Policy

システムは 2 種類の動作モデルをサポートします。

### Loose

編集ワークフロー向け。

特徴：

- 小さなタイミング揺らぎを許容
- パケットベース更新
- 高速再同期

用途：

- クライアントモニター
- 編集レビュー
- ソフトウェアタイムコード表示

---

### Tight

同期精度が重要な環境向け。

特徴：

- 連続したフレーム進行
- 安定したフレーム境界
- クロック制御された出力

用途：

- Unreal Engine
- LEDウォール
- バーチャルプロダクション
- 舞台照明
- メディアサーバー

---

# アーキテクチャ

INT Network Tools は 3 種類のデバイス役割で構成されます。

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

## Sender

ネットワークタイムコードを生成するデバイス。

例：

- **INT TimeCode Tool**
- DaVinci Resolve Workflow Integration Plugin

---

## Receiver

INTTC を受信し、タイムコードを解釈・表示するソフトウェア。

例：

- **SoftReceiver**

---

## Bridge

ネットワークタイムコードを **LTC 信号に変換するハードウェア**。

例：

- **RP2040 (Pico2) + W5500**
- INT TimeCode Bridge

---

# コア技術

## INTTC Protocol

INT Network Tools は **INTTC プロトコル**を使用してタイムコードを配信します。

特徴：

- UDP ベースのタイムコード配信
- 送信元識別
- フレームレート情報
- ドロップフレーム対応
- 複数タイムコードソース

デフォルトポート：

```
UDP 8001
```

---

# 設計原則

## 互換性を最優先

INT Network Tools は既存ワークフローとの互換性を重視しています。

**LTC 出力は常に第一級インターフェースとして扱われます。**

---

## ソフトウェア定義タイミング

Receiver は内部的に以下の時間モデルを使用します。

```
Packet Time
Resolved Time
Output Time
```

この構造により：

- ネットワークジッター耐性
- 出力の連続性維持
- 再同期制御
- 柔軟な同期戦略

を実現します。

---

# 使用例

## ポストプロダクション

- 編集レビュー
- クライアントモニター

## 放送

- LTC 配信
- タイムコード表示

## バーチャルプロダクション

- Unreal Engine 同期
- LED ウォール再生

## 舞台システム

- DMX 照明
- メディアサーバー同期

---

# リポジトリ構造

```
docs/
    philosophy/
    system/
    protocol/

sender/
    resolve-timecode-tool/

bridge/
    int-timecode-bridge/

receiver/
    softreceiver/
```

---

# プロジェクトステータス

現在は **アーキテクチャ設計および仕様策定段階**です。

開発対象：

- INTTC Protocol
- Resolve Sender
- INT TimeCode Bridge
- SoftReceiver

---

# ライセンス

未定

---

# Author

INT Network Tools DEV