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

- VMware 上の AD/DC は本ポートフォリオの Docker 範囲外（外部依存）  
  → ただし「認証基盤として利用している」前提は明記する
- 本書は「運用者が真似して再現できる」ようにコマンドをコピペ前提で記載する

---

## 3. 証跡採取の共通ルール（重要）

### 3-1. スクリーンショットに必ず含める情報

- 実行コマンド（ターミナル上に表示）
- 実行結果（OK/NG が分かる行）
- 対象コンテナ名（proxy1/proxy2/proxy3 等）
- 時刻（ログと突合できると強い）

### 3-2. ファイル命名ルール（推奨）

- p1-1_healthcheck.png
- p1-2_compose-ps.png
- p3-2_normal_vs_direct.png
- p6-4_triage.png

---

# P1：全体が動いている証拠（index.md 1章）

## 主張（何を証明するか）

多段プロキシ／認証／ログ／監視が統合された状態で稼働しており、  
起動確認・状態確認をスクリプトで再現可能な形で実施できることを示す。

---

## P1-1. Health Check 出力
<a id="p1-1"></a>

### 実行（WSL2）

    cd /home/login00/multiproxy
    ./scripts/multiproxy_health_all.sh

### 判定基準

- Proxy / Auth / Logging / Monitoring など各カテゴリが [OK]

### 撮るもの（証跡）

- images/healthcheck-output.png

---

## P1-2. コンテナ一覧（docker compose ps）
<a id="p1-2"></a>

### 実行（WSL2）

    cd /home/login00/multiproxy
    docker compose ps

### 判定基準

- 主要コンテナが Up
- 可能なものは healthy

### 撮るもの（証跡）

- images/docker-ps.png

---

# P2：アーキテクチャ／技術スタック（index.md 2章）

## P2-1. アーキテクチャ図と稼働コンポーネントの一致
<a id="p2-1"></a>

### 目的

設計図と実稼働が一致している（机上の空論ではない）ことを示す。

### 撮るもの（証跡）

- images/architecture.png
- images/docker-ps-arch-match.png

---

## P2-2. 技術スタック（バージョン）の確認
<a id="p2-2"></a>

### P2-2-1. バージョン一覧
<a id="p2-2-1"></a>

    cd /home/login00/multiproxy
    ./scripts/show-all-versions.sh

- 証跡：images/version-matrix.png

### P2-2-2. Squid の個別確認
<a id="p2-2-2"></a>

    docker exec -it proxy1 squid -v

- 証跡：images/squid-version.png

---

# P3：PAC / WPAD（通常 vs DIRECT）（index.md 3章）

## P3-1. PAC の内容確認
<a id="p3-1"></a>

### 実施（Windows クライアント）

- ブラウザで直接参照：

    http://wpad.ad.lan:8080/wpad.dat

### 判定基準

- PAC 内に以下が含まれる  
  - PROXY proxy1.ad.lan:3128  
  - PROXY proxy2.ad.lan:3131  
  - DIRECT

### 撮るもの（証跡）

- images/wpad-dat.png

---

## P3-2. 経路差のログ確認（NORMAL vs P2-DIRECT）
<a id="p3-2"></a>

### 目的

PAC により選択された経路差が、Proxy ログ差として確認できることを示す。

### クライアント側アクセス（例）

- NORMAL：https://www.google.com
- P2-DIRECT：https://www.wikipedia.org
- ROUTER-DIRECT（参考）：https://www.netflix.com

### Proxy 側ログ確認（WSL2）

#### NORMAL（例：google.com）

    docker compose exec -T proxy1 sh -lc \
      "grep -aRInE 'google\.com' /var/log/squid/access*.log 2>/dev/null | tail -n 3"

    docker compose exec -T proxy2 sh -lc \
      "grep -aRInE 'google\.com' /var/log/squid/access*.log 2>/dev/null | tail -n 3"

    docker compose exec -T proxy3 sh -lc \
      "grep -aRInE 'google\.com' /var/log/squid/access*.log 2>/dev/null | tail -n 3"

#### P2-DIRECT（例：wikipedia.org）

    docker compose exec -T proxy1 sh -lc \
      "grep -aRInE 'wikipedia(\.org)?' /var/log/squid/access*.log 2>/dev/null | tail -n 3 || true"

    docker compose exec -T proxy2 sh -lc \
      "grep -aRInE 'wikipedia(\.org)?' /var/log/squid/access*.log 2>/dev/null | tail -n 3"

    docker compose exec -T proxy3 sh -lc \
      "grep -aRInE 'wikipedia(\.org)?' /var/log/squid/access*.log 2>/dev/null | tail -n 3"

### 判定基準（重要）

- NORMAL：proxy1 / proxy2 / proxy3 全部にログが出る
- P2-DIRECT：proxy1 だけ出ない（proxy2 / proxy3 のみ）
- ROUTER-DIRECT：全部出ない（参考）

### 撮るもの（証跡）

- images/pac-flow-normal-vs-direct.png

---

# P4：SSLBump / stunnel / WSL2 mirrored（index.md 4章）

## P4-1：SSLBump（経路別の復号点）
<a id="p4-1"></a>

### P4-1-1. HTTPS & 証明書
<a id="p4-1-1"></a>

- Windows クライアントで HTTPS サイトへアクセス
- 証明書発行者が Multiproxy Root CA である画面を撮影

- 証跡：images/sslbump-cert.png

### P4-1-2. 通常経路：Proxy1 が復号しているログ
<a id="p4-1-2"></a>

    docker compose exec -T proxy1 sh -lc '
    LOGS="$(ls -1 /var/log/squid/access*.log 2>/dev/null || true)"
    PAT="(https://[^ ]*google\.com/|CONNECT ([^ ]*\.)?google\.com:443)"
    echo "### Proxy1 SSLBump evidence (google.com) : latest 3"
    grep -aRInE "$PAT" $LOGS 2>/dev/null | grep -a "bump=bump" | tail -n 3
    '

- 証跡：images/sslbump-proxy1-log.png

#### P4-1-3. DIRECT経路：Proxy2 が復号しているログ
<a id="p4-1-3"></a>

    docker compose exec -T proxy2 sh -lc '
    LOG=/var/log/squid/access_3131.log
    grep -aE "wikipedia\.org" "$LOG" 2>/dev/null | grep -a "bump=bump" | tail -n 3
    '

- 証跡：images/sslbump-proxy2-log.png

---

## P4-2：stunnel による中継暗号化
<a id="p4-2"></a>

### P4-2-1. openssl s_client による TLS 確認
<a id="p4-2-1"></a>

- 判定基準（同一画面に写す）  
  - CONNECTION ESTABLISHED  
  - TLSv1.3  
  - Peer certificate

- 証跡：images/stunnel-sclient.png

### P4-2-2. stunnel ログでの裏付け
<a id="p4-2-2"></a>

    docker compose logs --no-color --tail=200 proxy2-3-stunnel | tail -n 50
    docker compose logs --no-color --tail=200 proxy3-stunnel | tail -n 50

- 証跡：images/stunnel-log.png

---

## P4-3：WSL2 mirrored mode（同一L2）
<a id="p4-3"></a>

### P4-3-1. IP が同一セグメントである証拠
<a id="p4-3-1"></a>

- Windows / WSL2 / AD/DC / dnsmasq の IP を並べて撮影
- 証跡：images/wsl2-mirrored-ip.png

### P4-3-2. ドメイン参加・Kerberos チケット確認
<a id="p4-3-2"></a>

- Windows クライアントで以下を撮影  
  - whoami  
  - systeminfo  
  - klist

- 証跡：images/ad-domain-kerberos.png

---

# P6：ログ・監視（Observability）（index.md 6章）

## P6-0. 想定インシデント（例：deny増加）
<a id="p6-0"></a>

- 想定  
  - ユーザから「facebook が見れない」  
  - 運用側で deny が急増している

- この章で示すこと  
  - Zabbix（兆候検知）  
  - Grafana Loki（傾向把握）  
  - Graylog（原因特定）

---

## P6-1. Zabbix（兆候検知）
<a id="p6-1"></a>

- 証跡：images/zabbix-hosts.png
- 証跡：images/zabbix-proxy1-graph.png

## P6-2. Grafana Explore（Loki）
<a id="p6-2"></a>

    {container="proxy1"} |= "TCP_DENIED"

- 証跡：images/grafana-proxy-deny.png

## P6-3. Graylog 検索（原因特定）
<a id="p6-3"></a>

- 推奨クエリ例  
  - TCP_DENIED AND facebook.com  
  - TCP_DENIED  
  - source:172.18.0.21 AND TCP_DENIED

- 証跡：images/graylog-search.png

## P6-4. まとめ証跡（1枚で運用フローが分かる）
<a id="p6-4"></a>

- 構成（例）  
  - 左：Zabbix（ピーク時間）  
  - 中：Grafana（deny増加）  
  - 右：Graylog（拒否理由）

- 証跡：images/p6-observability-triage.png

---

# P7：自動化（再現性）（index.md 7章）

## P7-1. dnsmasq + PAC ヘルスチェック
<a id="p7-1"></a>

    cd /home/login00/multiproxy/scripts
    ./dnsmasq_pac_healthcheck.sh

- 証跡：images/dnsmasq-pac-healthcheck.png

## P7-2. 全体ヘルスチェック（再掲）
<a id="p7-2"></a>

- P1-1 を再利用可
- 証跡：images/healthcheck-output.png

---

# P8：想定外動作の切り分け（WPAD短縮名問題）（index.md 8章）

## P8-1. 短縮名がNG、FQDNがOK
<a id="p8-1"></a>

### PowerShell

    nslookup wpad
    ping wpad
    Test-NetConnection wpad -Port 8080

### ブラウザ

    http://wpad.ad.lan:8080/wpad.dat

- 証跡：images/wpad-shortname-issue.png

---

## 付録：証跡の貼り込みテンプレ（任意）

- P1：Healthcheck vs compose ps → P1_compare.png
- P2：Architecture vs containers → P2-1_compare.png
- P6：Zabbix vs Grafana vs Graylog → P6_triage.png
