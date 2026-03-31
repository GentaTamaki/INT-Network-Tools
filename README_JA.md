# INT Platform
クリエイティブ制作システム向けネットワーク同期インフラストラクチャ

INT Platform は、現代のメディア制作ワークフローのためのプロダクション同期インフラストラクチャです。

**クリエイティブワークフロー向けプロダクション同期基盤**

INT Platform は、ネットワーク環境でタイムコードを送信・解釈・配信するためのシステムアーキテクチャです。  
従来の **LTC（Linear Timecode）ベースのワークフロー**との互換性を保ちながら、ソフトウェア中心の制作環境と既存のプロフェッショナル機器を接続することを目的としています。

このプロジェクトは、放送・ポストプロダクション・舞台・バーチャルプロダクションなどで使用されている **既存のタイミングインフラ**と、現代のソフトウェア制作環境の橋渡しを行うことを目標としています。

INTTC は、ソフトウェアタイムラインと LTC ベースの同期システムを橋渡しする **ネットワークネイティブなタイムコードレイヤー**として設計されています。

---

# 三層モデル（Three-Layer Model）

INT Platform は、同期インフラを **レイヤーモデル**として理解することもできます。

```
Software Timeline Layer
        ↓
Sender Layer
        ↓
Distribution Layer
        ↓
Receiver / Hardware Layer
```

実際のワークフローでは次のような構造になります。

```
Editing / Playback Software
        ↓
Sender
        ↓
INT Distributor
        ↓
Receiver / Bridge
        ↓
LTC / Legacy / External Sync Systems
```

このモデルは、プラットフォーム内での責務を明確にします。

- タイムライン状態を **どこで生成するのか**（Software Timeline Layer）
- タイムライン状態を **どこでネットワークへ送信するのか**（Sender Layer）
- タイミング情報を **どこで配信・ルーティングするのか**（Distribution Layer）
- タイムコードを **どこで表示・解釈・変換するのか**（Receiver / Hardware Layer）

---

# ドキュメント
Contribution guidelines: see [CONTRIBUTING.md](CONTRIBUTING.md)

## Philosophy（設計思想）

- English: [INT_Philosophy_EN.md](doc/philosophy/INT_Philosophy_EN.md)
- 日本語: [INT_Philosophy_JA.md](doc/philosophy/INT_Philosophy_JA.md)

## System Specification（システム仕様）

- English: [INT_System_Spec_v0.1_EN.md](doc/system/INT_System_Spec_v0.1_EN.md)
- 日本語: [INT_System_Spec_v0.1_JA.md](doc/system/INT_System_Spec_v0.1_JA.md)

## INTTC Protocol（プロトコル仕様）

- English: [INTTC_Protocol_v1.2_EN.md](doc/protocol/INTTC_Protocol_v1.2_EN.md)
- 日本語: [INTTC_Protocol_v1.2_JA.md](doc/protocol/INTTC_Protocol_v1.2_JA.md)

---

# プロジェクトの目的

INT Platform は以下を目標としています。

- **ネットワークネイティブなタイムコード配信**
- **既存の LTC ハードウェアとの互換性維持**
- **編集用途とフレーム同期用途の両方に対応**
- 将来の同期技術（PTP / GPS など）への拡張基盤

---

# 基本コンセプト

## ネットワークタイムコードレイヤー

INT Platform は既存の同期システムを置き換えるのではなく、  
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

INT Platform のアーキテクチャは **Sender → Distributor → Receiver** モデルとして理解できます。

```
INT Platform
│
├─ Protocol
│   └─ INTTC
│
├─ Sender
│   ├─ Resolve Sender
│   │   └─ INT TimeCode Tool
│   ├─ Media Composer Sender
│   ├─ Premiere Sender
│   └─ Pro Tools Sender
│
├─ Distributor
│   └─ INT Distributor
│
└─ Receiver
    ├─ INT Viewer
    │   └─ Software UI / Debug Tools
    ├─ INT Monitor
    │   └─ Hardware Timecode Display
    └─ INT Bridge
        └─ LTC Output → Legacy Devices
```

このモデルでは、**Sender** が編集ソフトウェアなどからタイムライン状態を生成し、  
その情報を **INT Distributor** に送信します。

Distributor はネットワーク配信の中心となり、すべての **Receiver** にタイミング情報を配信します。
Receiver は Distributor からタイムコード情報を受信し、表示・監視・または LTC などの既存同期信号へ変換します。

この分離により、Resolve / Media Composer / Premiere / Pro Tools など複数の Sender 実装をサポートしながら、  
ソフトウェアビューア・ハードウェア表示装置・LTC 変換デバイスなど様々な Receiver を同一プラットフォーム上で構成できます。

# コア技術

## INTTC Protocol

INT Platform は **INTTC プロトコル**を使用してタイムコードを配信します。

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

INT Platform は既存ワークフローとの互換性を重視しています。

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
doc/
    philosophy/
    system/
    protocol/

sender/
    resolve-timecode-tool/

bridge/
    int-timecode-bridge/

receiver/
    viewer/
```

---

# プロジェクトステータス

現在は **アーキテクチャ設計および仕様策定段階**です。

開発対象：

- INTTC Protocol
- Resolve Sender
- INT TimeCode Bridge
- INT Viewer

---

# ライセンス

未定

---

# Author

INT Platform DEV