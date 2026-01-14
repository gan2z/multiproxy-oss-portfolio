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

本ドキュメントは、ポートフォリオ本文（`index.md`）で主張している内容を、  
**スクリーンショット／ログ／図で裏付ける**ための取得計画（Runbook）です。

- **何を証明するか**
- **どこで何を実行するか**
- **どのログを確認するか**
- **何を撮れば面接官が納得するか**

を **試験ID単位**で整理します。

> 方針：  
> - `index.md` は「設計思想と構造（主張）」  
> - 本ドキュメントは「動作する証拠（証跡）」  
> を提示します。

---

## 1. index.md章と検証観点（試験ID）対応表

index.md 各章が **どの試験IDの証跡で裏付けられるか** を対応付けています。

<div class="table-compact" id="map-table">
  <div class="table-wrap">
    <table class="map-table">
      <thead>
        <tr>
          <th>index.md章</th>
          <th>試験ID</th>
          <th>何を証明するか（スクショ／ログ）</th>
          <th>想定ファイル名</th>
        </tr>
      </thead>
      <tbody>

        <tr>
          <td class="col-chapter"><a href="./#1-プロジェクト概要summary">1. プロジェクト概要</a></td>
          <td class="col-id"><a href="#p1-1">P1-1</a>, <a href="#p1-2">P1-2</a></td>
          <td>全体稼働（ヘルスチェック／全コンテナ稼働）</td>
          <td class="col-files">
            <a href="./images/healthcheck-output.png" target="_blank"><code>healthcheck-output.png</code></a>,
            <a href="./images/docker-ps.png" target="_blank"><code>docker-ps.png</code></a>
          </td>
        </tr>

        <tr>
          <td class="col-chapter"><a href="./#2-システムアーキテクチャ全体像">2. システムアーキテクチャ</a></td>
          <td class="col-id"><a href="#p2-1">P2-1</a></td>
          <td>構成図と実稼働コンポーネントの一致</td>
          <td class="col-files">
            <a href="./images/architecture.png" target="_blank"><code>architecture.png</code></a>,
            <a href="./images/docker-ps-arch-match.png" target="_blank"><code>docker-ps-arch-match.png</code></a>
          </td>
        </tr>

        <tr>
          <td class="col-chapter"><a href="./#構成要素すべて-oss">2. 技術スタック</a></td>
          <td class="col-id"><a href="#p2-2-1">P2-2-1</a>, <a href="#p2-2-2">P2-2-2</a></td>
          <td>主要OSSの実行中バージョン取得と可視化</td>
          <td class="col-files">
            <a href="./images/version-matrix.png" target="_blank"><code>version-matrix.png</code></a>,
            <a href="./images/squid-version.png" target="_blank"><code>squid-version.png</code></a>
          </td>
        </tr>

        <tr>
          <td class="col-chapter"><a href="./#3-通信経路と設計構成変更点">3. PAC / WPAD</a></td>
          <td class="col-id"><a href="#p3-1">P3-1</a>, <a href="#p3-2">P3-2</a></td>
          <td>PAC取得・経路切替（通常／DIRECT）</td>
          <td class="col-files">
            <a href="./images/wpad-dat.png" target="_blank"><code>wpad-dat.png</code></a>,
            <a href="./images/pac-flow-normal-vs-direct.png" target="_blank"><code>pac-flow-normal-vs-direct.png</code></a>
          </td>
        </tr>

        <tr>
          <td class="col-chapter"><a href="./#4-1-復号sslbump位置の整理">4-1. SSLBump</a></td>
          <td class="col-id"><a href="#p4-1-1">P4-1-1</a>〜<a href="#p4-1-3">P4-1-3</a></td>
          <td>経路別の復号境界（通常／DIRECT）</td>
          <td class="col-files">
            <a href="./images/sslbump-cert.png" target="_blank"><code>sslbump-cert.png</code></a>,
            <a href="./images/sslbump-proxy1-log.png" target="_blank"><code>sslbump-proxy1-log.png</code></a>,
            <a href="./images/sslbump-proxy2-log.png" target="_blank"><code>sslbump-proxy2-log.png</code></a>
          </td>
        </tr>

        <tr>
          <td class="col-chapter"><a href="./#4-2-プロキシ間通信の暗号化">4-2. stunnel</a></td>
          <td class="col-id"><a href="#p4-2-1">P4-2-1</a>, <a href="#p4-2-2">P4-2-2</a></td>
          <td>Proxy間TLS（中継暗号化）</td>
          <td class="col-files">
            <a href="./images/stunnel-sclient.png" target="_blank"><code>stunnel-sclient.png</code></a>,
            <a href="./images/stunnel-log.png" target="_blank"><code>stunnel-log.png</code></a>
          </td>
        </tr>

        <tr>
          <td class="col-chapter"><a href="./#4-3-ネットワーク前提条件">4-3. WSL2 mirrored</a></td>
          <td class="col-id"><a href="#p4-3-1">P4-3-1</a>, <a href="#p4-3-2">P4-3-2</a></td>
          <td>同一L2前提での Kerberos / DNS / WPAD 成立</td>
          <td class="col-files">
            <a href="./images/wsl2-mirrored-ip.png" target="_blank"><code>wsl2-mirrored-ip.png</code></a>,
            <a href="./images/ad-domain-join.png" target="_blank"><code>ad-domain-join.png</code></a>
          </td>
        </tr>

        <tr>
          <td class="col-chapter"><a href="./#5-3-認証基盤ad--ldap--kerberosの切り分け">5-3. 認証</a></td>
          <td class="col-id"><a href="#p5-3-1">P5-3-1</a>〜<a href="#p5-3-3">P5-3-3</a></td>
          <td>認証フロー・切り分け</td>
          <td class="col-files">
            <a href="./images/kerberos-flow.png" target="_blank"><code>kerberos-flow.png</code></a>,
            <a href="./images/ldapwhoami.png" target="_blank"><code>ldapwhoami.png</code></a>,
            <a href="./images/proxy-auth-log.png" target="_blank"><code>proxy-auth-log.png</code></a>
          </td>
        </tr>

        <tr>
          <td class="col-chapter"><a href="./#6-ログ監視observability">6. ログ・監視</a></td>
          <td class="col-id"><a href="#p6-1">P6-1</a>〜<a href="#p6-3">P6-3</a></td>
          <td>兆候→絞り込み→原因特定（運用導線）</td>
          <td class="col-files">
            <a href="./images/zabbix-hosts.png" target="_blank"><code>zabbix-hosts.png</code></a>,
            <a href="./images/grafana-proxy-deny.png" target="_blank"><code>grafana-proxy-deny.png</code></a>,
            <a href="./images/graylog-search.png" target="_blank"><code>graylog-search.png</code></a>
          </td>
        </tr>

        <tr>
          <td class="col-chapter"><a href="./#7-自動化運用性再現性の確保">7. 自動化</a></td>
          <td class="col-id"><a href="#p7-1">P7-1</a>, <a href="#p7-2">P7-2</a></td>
          <td>再現性ある起動・検証・復旧</td>
          <td class="col-files">
            <a href="./images/dnsmasq-pac-healthcheck.png" target="_blank"><code>dnsmasq-pac-healthcheck.png</code></a>,
            <a href="./images/healthcheck-output.png" target="_blank"><code>healthcheck-output.png</code></a>
          </td>
        </tr>

      </tbody>
    </table>
  </div>
</div>

---

## 2. 共通前提（検証環境の条件）

### 2-1. 必須前提

- WSL2 上で `docker compose` が起動済み（主要コンテナが `Up` / `healthy`）
- Windows クライアントは AD 参加済み
- WPAD/PAC を取得できる状態（自動取得 or 直接参照）
- 証跡画像は `images/` に格納して参照する

### 2-2. 重要な注意点（読み手に優しい説明）

- VMware 上の AD/DC は **本ポートフォリオの Docker 範囲外**（外部依存）  
  → ただし「認証基盤として利用している」前提は明記する
- 本書は「運用者が真似して再現できる」ように  
  **コマンドをコピペ前提で記載**する

---

## 3. 証跡採取の共通ルール（重要）

### 3-1. スクリーンショットに必ず含める情報

- 実行コマンド（ターミナル上に表示）
- 実行結果（OK/NG が分かる行）
- 対象コンテナ名（proxy1/proxy2/proxy3 等）
- 時刻（ログと突合できると強い）

### 3-2. ファイル命名ルール（推奨）

- `p1-1_healthcheck.png`
- `p1-2_compose-ps.png`
- `p3-2_normal_vs_direct.png`
- `p6-4_triage.png`

---

# P1：全体が動いている証拠（index.md 1章）

## 主張（何を証明するか）

多段プロキシ／認証／ログ／監視が **統合された状態で稼働**しており、  
起動確認・状態確認をスクリプトで **再現可能な形で実施できる**ことを示す。

---

## P1-1. Health Check 出力
<a id="p1-1"></a>

### 実行（WSL2）

```bash
cd /home/login00/multiproxy
./scripts/multiproxy_health_all.sh
