

# INT Platform アーキテクチャ

## 1. 概要

INT Platform は、ネットワークベースでタイムコードを配信するシステムであり、
現代のソフトウェア制作環境と、LTC などの従来型同期インフラを接続することを
目的としています。

このプラットフォームは責務を以下の 3 層に分離しています。

- トランスポートプロトコル（INTTC）
- システムタイミングアーキテクチャ
- デバイス実装

本ドキュメントでは INT Platform の高レベルアーキテクチャと、
各コンポーネントがどのように相互作用するかを説明します。

---

## 2. システムロール

INT Platform はいくつかの論理的ロールを定義します。

### Sender

Sender は INTTC パケットを生成し、システムの権威的なタイムライン状態を
ネットワークに公開します。

代表的な Sender の例：

- DaVinci Resolve プラグイン
- 再生制御ソフトウェア
- タイムラインベースの制作ツール

Sender はタイム情報をネットワークへ配信します。

### Receiver

Receiver は INTTC パケットを受信し、ローカルのタイミングモデルを用いて
タイムライン状態を再構築します。

Receiver は次のようなシステムに実装される可能性があります。

- モニタリングアプリケーション
- ステージ表示システム
- 同期ツール
- 診断ユーティリティ

### Bridge

Bridge はネットワーク上のタイム情報を物理的な同期信号へ変換します。

代表的な出力：

- LTC
- audio LTC
- リファレンス同期信号

Bridge デバイスにより、従来の同期機器も INT ベースのワークフローに
参加することができます。

---

## 3. データフロー

典型的な INT Platform の信号フローは次のようになります。

```
Timeline Software
      ↓
INTTC Sender
      ↓
Network (UDP)
      ↓
Receiver
      ↓
Bridge (optional)
      ↓
LTC / Display / Monitoring
```

Sender は INTTC プロトコルを使用してタイムライン状態をネットワークへ
配信します。Receiver はその情報を解釈し、表示・監視・同期などの
出力を生成します。

---

## 4. タイミングモデル

INT Platform は時間処理を次の 3 層に分離しています。

```
Sender Timeline
      ↓
Packet Time (INTTC transport)
      ↓
Resolved Time (receiver interpretation)
      ↓
Output Time (signal generation)
```

このレイヤー構造により、ネットワークパケットの到着タイミングに
ばらつきがあっても、Receiver は安定した出力タイミングを維持できます。

---

## 5. INTTC との関係

INTTC は INT Platform が使用するトランスポートプロトコルです。

```
INTTC Protocol
      ↓
INT Platform Architecture
      ↓
Device Implementations
```

INTTC はタイミング情報のエンコード方法と伝送方法を定義します。

INT Platform System Specification は、その情報をシステムがどのように
解釈・処理するかを定義します。

---

## 6. 実装例

INT Platform の構成例：

- NLE プラグインが Sender として動作
- ネットワーク Receiver がタイムコード表示
- ハードウェア Bridge が LTC を生成

これらのコンポーネントを組み合わせることで、次のような環境で
ワークフローを構築できます。

- ポストプロダクション
- 放送システム
- ステージシステム
- バーチャルプロダクション

---

## Status

アーキテクチャ説明のドラフト。

INT Platform の実装が進むにつれて、このドキュメントは
更新される可能性があります。