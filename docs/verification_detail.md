---
title: 検証観点 & スクリーンショット取得チェックリスト（詳細版）
layout: default
---

# 検証観点 & スクリーンショット取得チェックリスト（詳細版）
（OSS Multi-Layer Proxy & Authentication System）

Version: 2025-01-15  
Author: gan2

---

## 0. 本ドキュメントの目的（詳細版）

本ドキュメントは、ポートフォリオ本文（`index.md`）および  
Verification ページで示した主張を、  
**スクリーンショット／ログ／図で裏付けるための取得計画（Runbook）**です。

- 何を証明するか  
- どこで何を実行するか  
- どのログを確認するか  
- 何を撮れば第三者が納得できるか  

を **試験ID単位**で整理します。

> 方針：  
> - `index.md` / `Verification`：設計思想・結論  
> - 本ドキュメント：それを裏付ける **再現可能な証跡取得手順**

---

## 1. index.md 章と検証観点（試験ID）対応表

<div class="table-compact">
  <div class="table-wrap">
    <table class="map-table">
      <thead>
        <tr>
          <th>章</th>
          <th>試験ID</th>
          <th>何を証明するか</th>
          <th>想定ファイル名</th>
        </tr>
      </thead>
      <tbody>

        <tr>
          <td>1. 概要</td>
          <td>P1-1, P1-2</td>
          <td>システム全体が稼働していること</td>
          <td>
            healthcheck-output.png,<br>
            docker-ps.png
          </td>
        </tr>

        <tr>
          <td>2. アーキテクチャ</td>
          <td>P2-1</td>
          <td>構成図と実稼働コンテナが一致していること</td>
          <td>
            architecture.png,<br>
            docker-ps-arch-match.png
          </td>
        </tr>

        <tr>
          <td>2. 技術スタック</td>
          <td>P2-2-1, P2-2-2</td>
          <td>主要 OSS の実行中バージョンを把握していること</td>
          <td>
            version-matrix.png,<br>
            squid-version.png
          </td>
        </tr>

        <tr>
          <td>3. 経路制御（PAC）</td>
          <td>P3-1, P3-2</td>
          <td>PAC による経路切替とログ差での検証</td>
          <td>
            pac-file-view.png,<br>
            pac-flow-normal-vs-direct.png
          </td>
        </tr>

        <tr>
          <td>4. 復号 / 暗号化</td>
          <td>P4-1, P4-2</td>
          <td>SSLBump と stunnel の責務分離</td>
          <td>
            sslbump-cert.png,<br>
            sslbump-proxy-log.png,<br>
            stunnel-log.png
          </td>
        </tr>

        <tr>
          <td>6. 可観測性</td>
          <td>P6-1〜P6-3</td>
          <td>Loki によるログ追跡と原因特定</td>
          <td>
            grafana-proxy-deny.png
          </td>
        </tr>

        <tr>
          <td>7. 自動化</td>
          <td>P7-1, P7-2</td>
          <td>起動・確認・復旧の再現性</td>
          <td>
            healthcheck-output.png
          </td>
        </tr>

      </tbody>
    </table>
  </div>
</div>

---

## 2. 共通前提（検証環境）

### 2-1. 前提条件

- WSL2 上で `docker compose` が起動済み
- 主要コンテナが `Up` / `healthy`
- Windows クライアントは AD 参加済み
- **PAC はクライアントで URL を直接指定して利用**
- 証跡画像は `images/` 配下に保存

※ WPAD（自動検出）は使用しない

---

## 3. 証跡取得の共通ルール

### 3-1. スクリーンショットに含める要素

- 実行コマンド
- 実行結果（成功が分かる行）
- 対象コンテナ名
- 可能であれば時刻

### 3-2. ファイル命名ルール（例）

- p1-1_healthcheck.png
- p3-2_normal_vs_direct.png
- p6-2_loki_deny.png

---

# P1：全体稼働の証明（index.md 1章）

## P1-1. Health Check
<a id="p1-1"></a>

    cd /home/login00/multiproxy
    ./scripts/multiproxy_health_all.sh

**判定基準**  
- 全カテゴリが `[OK]`

**証跡**  
- images/healthcheck-output.png

---

## P1-2. コンテナ稼働状態
<a id="p1-2"></a>

    docker compose ps

**判定基準**  
- 主要コンテナが Up / healthy

**証跡**  
- images/docker-ps.png

---

# P2：アーキテクチャと技術スタック（index.md 2章）

## P2-1. 構成図と稼働状態の一致
<a id="p2-1"></a>

**証跡**  
- images/architecture.png  
- images/docker-ps-arch-match.png

---

## P2-2. 実行中バージョンの確認
<a id="p2-2"></a>

### P2-2-1. 一覧取得

    ./scripts/show-all-versions.sh

- images/version-matrix.png

### P2-2-2. Squid 個別確認

    docker exec -it proxy1 squid -v

- images/squid-version.png

---

# P3：PAC による経路制御（index.md 3章）

## P3-1. PAC ファイル確認
<a id="p3-1"></a>

- ブラウザで PAC ファイルを直接参照
- クライアントで URL 指定して利用していることを確認

**証跡**  
- images/pac-file-view.png

---

## P3-2. 経路差のログ確認
<a id="p3-2"></a>

- NORMAL：proxy1 → proxy2 → proxy3
- P2-DIRECT：proxy2 → proxy3

**判定基準**  
- proxy1 のログ有無で経路が判別できる

**証跡**  
- images/pac-flow-normal-vs-direct.png

---

# P4：復号と中継暗号化（index.md 4章）

## P4-1. SSLBump の適用位置
<a id="p4-1"></a>

- 証明書発行者が Multiproxy Root CA

**証跡**  
- images/sslbump-cert.png

---

## P4-2. stunnel による中継TLS
<a id="p4-2"></a>

- Proxy 間通信が TLS 化されている

**証跡**  
- images/stunnel-log.png

---

# P6：ログ追跡と切り分け（Loki）（index.md 6章）

## P6-1. deny 増加の検知
<a id="p6-1"></a>

- Grafana Explore（Loki）で deny ログを確認

    {container="proxy1"} |= "TCP_DENIED"

**証跡**  
- images/grafana-proxy-deny.png

---

## P6-2. 経路・復号点の特定
<a id="p6-2"></a>

- proxy1 / proxy2 / proxy3 のログ有無を突合
- 設計どおりの経路かを確認

---

## P6-3. 原因判断
<a id="p6-3"></a>

- PAC 条件
- SSLBump 適用位置
- stunnel 境界

を設計前提と照合し、原因を特定

---

# P7：自動化と再現性（index.md 7章）

## P7-1. 全体ヘルスチェック
<a id="p7-1"></a>

- P1-1 を再利用

## P7-2. 障害時の再実行
<a id="p7-2"></a>

- 同一手順で復旧可能であることを確認

---

## 付録：証跡整理テンプレ（任意）

- 全体稼働：P1-proof-all.png  
- 経路差：P3-normal-vs-direct.png  
- ログ追跡：P6-triage.png
