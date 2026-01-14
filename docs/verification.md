---
title: Verification（面接官向け：動作証跡の要点）
layout: default
---

# Verification（面接官向け：動作証跡の要点）
（OSS Multi-Layer Proxy & Authentication System）

Version: 2025-01-15  
Author: gan2

---

## 0. このページの見方（面接官向け）

このページは、index.md の主張を **「スクリーンショット／ログ」**で裏付ける証拠集です。  
**5分で全体→10分で深掘り**できるよう、見る順番を固定しています。

### ✅ まず見るべき3枚（5分コース）
1) **P1：全体が稼働している**（Health / docker ps）  
2) **P3：PACで経路が切り替わる**（NORMAL vs P2-DIRECT のログ差）  
3) **P4：復号点と中継TLSの分離**（SSLBump / stunnel）

> 方針：index.md は “設計思想と構造”、本ページは “動作する根拠” を提示します

---

## 1. index.md の主張 ↔ 証跡（試験ID）対応表（面接官用）

<div class="table-compact" id="map-table">
  <div class="table-wrap">
    <table class="map-table">
      <thead>
        <tr>
          <th>index.md章</th>
          <th>試験ID</th>
          <th>何を証明するか（結論）</th>
          <th>主要証跡（クリックで拡大）</th>
        </tr>
      </thead>
      <tbody>

        <tr>
          <td class="col-chapter"><a href="./#1-プロジェクト概要summary">1. 概要</a></td>
          <td class="col-id"><a href="#p1-1">P1-1</a>, <a href="#p1-2">P1-2</a></td>
          <td>多段Proxy/認証/ログ/監視が <strong>統合稼働</strong>し、再現可能</td>
          <td class="col-files">
            <a href="./images/P１：証跡_比較.png" target="_blank"><code>P１：証跡_比較.png</code></a>
          </td>
        </tr>

        <tr>
          <td class="col-chapter"><a href="./#2-システムアーキテクチャ全体像">2. アーキテクチャ</a></td>
          <td class="col-id"><a href="#p2-1">P2-1</a></td>
          <td>構成図と稼働コンテナが <strong>1:1対応</strong>している</td>
          <td class="col-files">
            <a href="./images/P2-1：証跡_比較.png" target="_blank"><code>P2-1：証跡_比較.png</code></a>
          </td>
        </tr>

        <tr>
          <td class="col-chapter"><a href="./#構成要素すべて-oss">2. 技術スタック</a></td>
          <td class="col-id"><a href="#p2-2-1">P2-2-1</a>, <a href="#p2-2-2">P2-2-2</a></td>
          <td>イメージタグではなく <strong>実バイナリのバージョン</strong>を把握</td>
          <td class="col-files">
            <a href="./images/P2-2：証跡_比較.png" target="_blank"><code>P2-2：証跡_比較.png</code></a>
          </td>
        </tr>

        <tr>
          <td class="col-chapter"><a href="./#3-通信経路と設計構成変更点">3. 経路制御（PAC）</a></td>
          <td class="col-id"><a href="#p3-1">P3-1</a>, <a href="#p3-2">P3-2</a></td>
          <td>PAC により <strong>通常 / DIRECT</strong> が切り替わり、ログで証明できる</td>
          <td class="col-files">
            <a href="./images/pac-flow-normal-vs-direct.png" target="_blank"><code>pac-flow-normal-vs-direct.png</code></a>
          </td>
        </tr>

        <tr>
          <td class="col-chapter"><a href="./#4-1-復号sslbump位置の整理">4. 復号（SSLBump）</a></td>
          <td class="col-id"><a href="#p4-1-1">P4-1-1</a>〜<a href="#p4-1-3">P4-1-3</a></td>
          <td>復号点を <strong>通常=Proxy1 / DIRECT=Proxy2</strong> に分離した証拠</td>
          <td class="col-files">
            <a href="./images/sslbump-cert.png" target="_blank"><code>sslbump-cert.png</code></a>,
            <a href="./images/sslbump-proxy1-log.png" target="_blank"><code>sslbump-proxy1-log.png</code></a>,
            <a href="./images/sslbump-proxy2-log.png" target="_blank"><code>sslbump-proxy2-log.png</code></a>
          </td>
        </tr>

        <tr>
          <td class="col-chapter"><a href="./#4-2-プロキシ間通信の暗号化">4. 中継TLS（stunnel）</a></td>
          <td class="col-id"><a href="#p4-2-1">P4-2-1</a>, <a href="#p4-2-2">P4-2-2</a></td>
          <td>Proxy間は <strong>stunnel によりTLS化</strong>され、暗号化境界が明確</td>
          <td class="col-files">
            <a href="./images/stunnel-sclient.png" target="_blank"><code>stunnel-sclient.png</code></a>,
            <a href="./images/stunnel-log.png" target="_blank"><code>stunnel-log.png</code></a>
          </td>
        </tr>

        <tr>
          <td class="col-chapter"><a href="./#4-3-ネットワーク前提条件">4. ネットワーク前提</a></td>
          <td class="col-id"><a href="#p4-3-1">P4-3-1</a>, <a href="#p4-3-2">P4-3-2</a></td>
          <td>Kerberos/WPAD/DNS の前提を満たすため <strong>WSL2 mirrored</strong> を採用</td>
          <td class="col-files">
            <a href="./images/wsl2-mirrored-ip.png" target="_blank"><code>wsl2-mirrored-ip.png</code></a>,
            <a href="./images/ad-domain-kerberos.png" target="_blank"><code>ad-domain-kerberos.png</code></a>
          </td>
        </tr>

        <tr>
          <td class="col-chapter"><a href="./#6-ログ監視observability">6. 可観測性</a></td>
          <td class="col-id"><a href="#p6-1">P6-1</a>〜<a href="#p6-4">P6-4</a></td>
          <td>兆候検知→絞り込み→原因当たり付けが <strong>GUI/ログで辿れる</strong></td>
          <td class="col-files">
            <a href="./images/p6-observability-triage.png" target="_blank"><code>p6-observability-triage.png</code></a>
          </td>
        </tr>

        <tr>
          <td class="col-chapter"><a href="./#7-自動化運用性再現性の確保">7. 自動化</a></td>
          <td class="col-id"><a href="#p7-1">P7-1</a>, <a href="#p7-2">P7-2</a></td>
          <td>壊れても戻せるよう <strong>起動/確認/復旧</strong> を手順化</td>
          <td class="col-files">
            <a href="./images/dnsmasq-pac-healthcheck.png" target="_blank"><code>dnsmasq-pac-healthcheck.png</code></a>,
            <a href="./images/healthcheck-output.png" target="_blank"><code>healthcheck-output.png</code></a>
          </td>
        </tr>

        <tr>
          <td class="col-chapter"><a href="./#8-学習成果本プロジェクトを通じて得られた理解">8. 切り分け力</a></td>
          <td class="col-id"><a href="#p8-1">P8-1</a></td>
          <td>想定外挙動を <strong>仕様×挙動</strong>で説明できる（WPAD短縮名）</td>
          <td class="col-files">
            <a href="./images/wpad-shortname-issue.png" target="_blank"><code>wpad-shortname-issue.png</code></a>
          </td>
        </tr>

      </tbody>
    </table>
  </div>
</div>

<p class="note"><em>
※ P5（学習ログ/失敗再現）は、環境へ影響を与える可能性があるため「当時ログが残っている場合のみ」掲載対象としています。
</em></p>

---

## 2. 面接官向け：まず見る「比較画像」ショーケース（最短で伝える）

### P1：全体が動いている（Health / Container 状態）
<a id="p1-showcase"></a>

<div style="text-align:center; margin: 1.2em 0;">
  <a href="./images/P１：証跡_比較.png" target="_blank">
    <img
      src="./images/P１：証跡_比較.png"
      alt="P1 証跡比較（Health Check / Container 状態）"
      style="width:100%; max-width:1200px; height:auto;
             border-radius:12px;
             box-shadow:0 6px 18px rgba(0,0,0,.15);
             cursor: zoom-in;"
    >
  </a>
</div>
<p style="text-align:center; font-size:.9em; opacity:.8;">※ 画像をクリックすると原寸で確認できます</p>

---

### P3：PACで経路が切り替わっている（NORMAL vs P2-DIRECT）
<a id="p3-showcase"></a>

<div style="text-align:center; margin: 1.2em 0;">
  <a href="./images/pac-flow-normal-vs-direct.png" target="_blank">
    <img
      src="./images/pac-flow-normal-vs-direct.png"
      alt="PAC 経路差分（NORMAL vs P2-DIRECT）"
      style="width:100%; max-width:1200px; height:auto;
             border-radius:12px;
             box-shadow:0 6px 18px rgba(0,0,0,.15);
             cursor: zoom-in;"
    >
  </a>
</div>
<p style="text-align:center; font-size:.9em; opacity:.8;">※ 画像をクリックすると原寸で確認できます</p>

---

### P4：復号（SSLBump）と中継TLS（stunnel）を分離している
<a id="p4-showcase"></a>

<div style="text-align:center; margin: 1.2em 0;">
  <a href="./images/P4：証跡_比較.png" target="_blank">
    <img
      src="./images/P4：証跡_比較.png"
      alt="P4 証跡比較（SSLBump / stunnel）"
      style="width:100%; max-width:1200px; height:auto;
             border-radius:12px;
             box-shadow:0 6px 18px rgba(0,0,0,.15);
             cursor: zoom-in;"
    >
  </a>
</div>
<p style="text-align:center; font-size:.9em; opacity:.8;">※ 画像をクリックすると原寸で確認できます</p>

---

## 3. 各試験ID（詳細：必要なら深掘り）

> ここから下は「面接で突っ込まれたときに、そのまま説明できる」用です。  
> **冒頭に結論 → その後に手順/ログ**の順で統一しています。

---

## P1：プロジェクト概要「全体が動いている」証拠（index.md 1章）

### 結論（何が言えるか）
<a id="p1"></a>

多段プロキシ／認証／ログ／監視が **統合された状態で稼働**しており、  
起動確認・状態確認を **スクリプトで再現可能**である。

---

### P1-1. Health Check 出力
<a id="p1-1"></a>

**実行**

    cd /home/login00/multiproxy
    ./scripts/multiproxy_health_all.sh

**確認内容**

- Proxy / Auth / Logging / Monitoring など各カテゴリが `[OK]`

**証跡**

- `healthcheck-output.png`

---

### P1-2. コンテナ一覧（docker compose ps）
<a id="p1-2"></a>

**実行**

    cd /home/login00/multiproxy
    docker compose ps

**確認内容**

- 主要コンテナが `Up`
- 可能なものは `healthy`

**証跡**

- `docker-ps.png`

<details>
  <summary><strong>（参考）カテゴリ別に整形して表示（任意）</strong></summary>

  <p style="margin: .5rem 0 0;">
    ※ 面接向けには「P１：証跡_比較.png」が最短です。必要に応じて補助として使います。
  </p>

  <pre style="margin-top:.6rem; padding:.8rem; overflow-x:auto; border:1px solid rgba(127,127,127,.25); border-radius:10px;"><code class="language-bash">cd /home/login00/multiproxy
docker compose ps</code></pre>
</details>

---

## P2：アーキテクチャ／技術スタック（index.md 2章）

### 結論（何が言えるか）
<a id="p2"></a>

設計したアーキテクチャと技術スタックが、  
**稼働中のコンテナ構成・ソフトウェア構成と一致**している。

---

### P2-1. アーキテクチャ図と稼働コンポーネントの一致
<a id="p2-1"></a>

**証跡（比較表示）**

- `P2-1：証跡_比較.png`

---

### P2-2. 技術スタック（ソフトウェアバージョン）の確認
<a id="p2-2"></a>

#### P2-2-1. バージョン一覧（全体）
<a id="p2-2-1"></a>

    cd /home/login00/multiproxy
    ./scripts/show-all-versions.sh

- 証跡：`version-matrix.png`

#### P2-2-2. 代表例（Squid）
<a id="p2-2-2"></a>

    docker exec -it proxy1 squid -v

- 証跡：`squid-version.png`

---

## P3：PAC / WPAD による経路制御（通常 vs P2-DIRECT）（index.md 3章）

### 結論（何が言えるか）
<a id="p3"></a>

PAC により「クライアント出口（Proxy1 / Proxy2 / DIRECT）」が切り替わり、  
**Proxyログの有無（どこを通ったか）で客観的に証明できる。**

---

### P3-1. PAC の内容確認
<a id="p3-1"></a>

**確認URL**

    http://wpad.ad.lan:8080/wpad.dat

**確認内容（例）**
- `PROXY proxy1.ad.lan:3128`
- `PROXY proxy2.ad.lan:3131`
- `DIRECT`

**証跡**
- `wpad-dat.png`

---

### P3-2. 経路差のログ確認（NORMAL vs P2-DIRECT）
<a id="p3-2"></a>

#### 目的
PAC により選択された経路差が、各Proxyのログ差として確認できる。

- NORMAL：Proxy1 → Proxy2 → Proxy3
- P2-DIRECT：Proxy2 → Proxy3（Proxy1 skip）
- ROUTER-DIRECT（参考）：Proxy非経由

---

#### クライアント側アクセス（例）
- NORMAL：`https://www.google.com`
- P2-DIRECT：`https://www.wikipedia.org`
- ROUTER-DIRECT（参考）：`https://www.netflix.com`（PAC無効/プロキシなしで比較）

---

#### Proxy側ログ確認（例）

**NORMAL（google.com）**

    docker compose exec -T proxy1 sh -lc \
      "grep -aRInE 'google\.com' /var/log/squid/access*.log 2>/dev/null | tail -n 3"

    docker compose exec -T proxy2 sh -lc \
      "grep -aRInE 'google\.com' /var/log/squid/access*.log 2>/dev/null | tail -n 3"

    docker compose exec -T proxy3 sh -lc \
      "grep -aRInE 'google\.com' /var/log/squid/access*.log 2>/dev/null | tail -n 3"

**P2-DIRECT（wikipedia.org）**

    docker compose exec -T proxy1 sh -lc \
      "grep -aRInE 'wikipedia(.org)?' /var/log/squid/access*.log 2>/dev/null | tail -n 3 || true"

    docker compose exec -T proxy2 sh -lc \
      "grep -aRInE 'wikipedia(.org)?' /var/log/squid/access*.log 2>/dev/null | tail -n 3"

    docker compose exec -T proxy3 sh -lc \
      "grep -aRInE 'wikipedia(.org)?' /var/log/squid/access*.log 2>/dev/null | tail -n 3"

---

#### 判定基準（面接での説明用）
- NORMAL：proxy1 / proxy2 / proxy3 にログが出る
- P2-DIRECT：proxy1 はログなし、proxy2 / proxy3 のみに出る
- ROUTER-DIRECT（参考）：全Proxyログなし

**証跡**
- `pac-flow-normal-vs-direct.png`

---

## P4：SSLBump / stunnel / WSL2 mirrored（index.md 4章）

### 結論（何が言えるか）
<a id="p4"></a>

- SSLBump は **1通信1回**の制約があるため、復号点を経路で分離  
- Proxy間の暗号化は Squid に混ぜず、**stunnel に責務分離**  
- Kerberos/WPAD/DNS の前提維持のため **WSL2 mirrored** を採用

---

## P4-1：SSLBump の適用範囲（経路別）（index.md 4-1）

#### P4-1-1. HTTPSアクセス & 証明書
<a id="p4-1-1"></a>

- HTTPSサイトへアクセスし、証明書の発行者が Multiproxy Root CA の画面
- 証跡：`sslbump-cert.png`

#### P4-1-2. 通常経路：Proxy1 が復号しているログ
<a id="p4-1-2"></a>

    docker compose exec -T proxy1 sh -lc '
    LOGS="$(ls -1 /var/log/squid/access*.log 2>/dev/null || true)"
    PAT="(https://[^ ]*google\.com/|CONNECT ([^ ]*\.)?google\.com:443)"
    grep -aRInE "$PAT" $LOGS 2>/dev/null | grep -a "bump=bump" | tail -n 3
    '

- 証跡：`sslbump-proxy1-log.png`

#### P4-1-3. DIRECT経路：Proxy2 が復号しているログ
<a id="p4-1-3"></a>

    docker compose exec -T proxy2 sh -lc '
    LOG=/var/log/squid/access_3131.log
    grep -aE "wikipedia\.org" "$LOG" 2>/dev/null | grep -a "bump=bump" | tail -n 3
    '

- 証跡：`sslbump-proxy2-log.png`

---

## P4-2：stunnel によるプロキシ間 TLS（中継暗号化）（index.md 4-2）

#### P4-2-1. openssl s_client による TLS 確認
<a id="p4-2-1"></a>

- 証跡：`stunnel-sclient.png`
- 観点：
  - `CONNECTION ESTABLISHED`
  - `TLSv1.3`
  - `Peer certificate` が stunnel 側

#### P4-2-2. stunnel ログ
<a id="p4-2-2"></a>

- 証跡：`stunnel-log.png`

---

## P4-3：WSL2 mirrored mode & AD/DC 連携（index.md 4-3）

#### P4-3-1. IP アドレス比較（同一セグメント）
<a id="p4-3-1"></a>

- Windows / WSL2 / AD/DC / dnsmasq が同一セグメントである証拠
- 証跡：`wsl2-mirrored-ip.png`

#### P4-3-2. ドメイン参加 & Kerberos の証拠
<a id="p4-3-2"></a>

- whoami / systeminfo / klist などで Kerberos が使われている証跡
- 証跡：`ad-domain-kerberos.png`

---

## P6：ログ・監視（Observability）（index.md 6章）

### 結論（何が言えるか）
<a id="p6"></a>

ログ（Loki/Graylog）と監視（Zabbix）で、  
**兆候検知 → 絞り込み → 原因当たり付け**の運用フローを説明できる。

---

### P6-1. Zabbix（兆候検知）
<a id="p6-1"></a>

- 証跡：`zabbix-hosts.png` / `zabbix-proxy1-graph.png`

### P6-2. Grafana Explore（Loki）
<a id="p6-2"></a>

- 証跡：`grafana-proxy-deny.png`

### P6-3. Graylog 検索（原因特定）
<a id="p6-3"></a>

- 証跡：`graylog-search.png`

### P6-4. まとめ比較（1枚で運用フロー）
<a id="p6-4"></a>

- Zabbix（時刻特定）→ Grafana（増加傾向）→ Graylog（具体通信）
- 証跡：`p6-observability-triage.png`

---

## P7：自動化（再現性）（index.md 7章）

### 結論（何が言えるか）
<a id="p7"></a>

手作業ではなく、スクリプトで **起動/確認/復旧**までを標準化し、  
「壊しても戻せる」状態を作っている。

---

### P7-1. dnsmasq + PAC ヘルスチェック
<a id="p7-1"></a>

    cd /home/login00/multiproxy/scripts
    ./dnsmasq_pac_healthcheck.sh

- 証跡：`dnsmasq-pac-healthcheck.png`

### P7-2. 全体ヘルスチェック（再掲）
<a id="p7-2"></a>

- 証跡：`healthcheck-output.png`

---

## P8：想定外動作の切り分け（WPAD 短縮名問題）（index.md 8章）

### 結論（何が言えるか）
<a id="p8"></a>

「なんか変」で終わらせず、仕様（WPAD保護）と挙動差を突き合わせ、  
原因を言語化して設計前提に落とし込める。

---

### P8-1. WPAD 短縮名問題の証跡（Windowsクライアント）
<a id="p8-1"></a>

PowerShell：

    nslookup wpad
    ping wpad
    Test-NetConnection wpad -Port 8080

ブラウザ：

    http://wpad.ad.lan:8080/wpad.dat

- 証跡：`wpad-shortname-issue.png`

---

## 付録：P5（学習ログ/失敗再現）は任意（必要な場合のみ）

<details>
  <summary><strong>P5（任意）：失敗→原因→改善の根拠（クリックで開く）</strong></summary>

  <p>
    P5 は「環境へ影響が出うる再現」が含まれるため、当時ログがある場合のみ掲載対象とします。
    面接で“なぜその設計にしたか”を深掘りされたときに提示する想定です。
  </p>

  <ul>
    <li><strong>P5-1：</strong>なぜ stunnel を分離したか（思考ログ）</li>
    <li><strong>P5-2：</strong>二重SSLBumpが破綻したログ（設計変更の根拠）</li>
    <li><strong>P5-3：</strong>認証をレイヤ別に切り分ける観点（Kerberos/LDAP）</li>
  </ul>

</details>
