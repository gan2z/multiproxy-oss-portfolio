---
title: 検証観点 & スクリーンショット取得チェックリスト
layout: default
---

# 検証観点 & スクリーンショット取得チェックリスト  
（OSS Multi-Layer Proxy & Authentication System）

Version: 2025-02  
Author: gan2

---

## 0. 本ドキュメントの目的

本ドキュメントは、ポートフォリオ本文（index.md）で主張している内容を、**スクリーンショット／ログ／図で裏付ける**ための取得計画です。  
「何を証明するか」「どこで何を実行するか」「何を撮るか」を試験ID単位で整理します。

> 方針：index.md は“設計思想と構造”、本ドキュメントは“動作する証拠”を提示する

---

## 1. index.mdの各章と検証観点（試験ID）の対応

index.md 各章が **どの検証観点（試験ID）を満たしているか** を、  
スクリーンショット／ログと対応付けて整理しています。
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
          <td class="col-id">
            <a href="#p1-1">P1-1</a>, <a href="#p1-2">P1-2</a>
          </td>
          <td>全体稼働（ヘルスチェック／全コンテナ稼働）</td>
          <td class="col-files">
            <a href="./images/healthcheck-output.png" target="_blank"><code>healthcheck-output.png</code></a>,
            <a href="./images/docker-ps.png" target="_blank"><code>docker-ps.png</code></a>
          </td>
        </tr>
        <tr>
          <td class="col-chapter"><a href="./#2-システムアーキテクチャ全体像">2. システムアーキテクチャ</a></td>
          <td class="col-id">
            <a href="#p2-1">P2-1</a>
          </td>
          <td>構成図と実稼働コンポーネントの一致</td>
          <td class="col-files">
            <a href="./images/architecture.png" target="_blank"><code>architecture.png</code></a>,
            <a href="./images/docker-ps-arch-match.png" target="_blank"><code>docker-ps-arch-match.png</code></a>
          </td>
        </tr>
        <tr>
          <td class="col-chapter"><a href="./#構成要素すべて-oss">2. 構成要素（技術スタック）</a></td>
          <td class="col-id">
            <a href="#p2-2-1">P2-2-1</a>, <a href="#p2-2-2">P2-2-2</a>
          </td>
          <td>
            主要 OSS の<strong>実行中バージョン取得と可視化</strong><br>
            （Docker イメージタグではなく、実バイナリのバージョン確認）
          </td>
          <td class="col-files">
            <a href="./images/version-matrix.png" target="_blank"><code>version-matrix.png</code></a>,
            <a href="./images/squid-version.png" target="_blank"><code>squid-version.png</code></a>
          </td>
        </tr>
        <tr>
          <td class="col-chapter"><a href="./#3-通信経路と設計構成変更点">3. PAC / WPAD</a></td>
          <td class="col-id">
            <a href="#p3-1">P3-1</a>, <a href="#p3-2">P3-2</a>
          </td>
          <td>PAC 取得・経路切替（通常／DIRECT）</td>
          <td class="col-files">
            <a href="./images/wpad-dat.png" target="_blank"><code>wpad-dat.png</code></a>,
            <a href="./images/pac-flow-normal-vs-direct.png" target="_blank"><code>pac-flow-normal-vs-direct.png</code></a>
          </td>
        </tr>
        <tr>
          <td class="col-chapter"><a href="./#4-1-復号sslbump位置の整理">4-1. SSLBump</a></td>
          <td class="col-id">
            <a href="#p4-1-1">P4-1-1</a>〜<a href="#p4-1-3">P4-1-3</a><br>
            （<a href="#p4-1-1">P4-1-1</a>, <a href="#p4-1-2">P4-1-2</a>, <a href="#p4-1-3">P4-1-3</a>）
          </td>
          <td>経路別の復号境界（通常／DIRECT）</td>
          <td class="col-files">
            <a href="./images/sslbump-cert.png" target="_blank"><code>sslbump-cert.png</code></a>,
            <a href="./images/sslbump-proxy1-log.png" target="_blank"><code>sslbump-proxy1-log.png</code></a>,
            <a href="./images/sslbump-proxy2-log.png" target="_blank"><code>sslbump-proxy2-log.png</code></a>
          </td>
        </tr>
        <tr>
          <td class="col-chapter"><a href="./#4-2-プロキシ間通信の暗号化">4-2. stunnel</a></td>
          <td class="col-id">
            <a href="#p4-2-1">P4-2-1</a>, <a href="#p4-2-2">P4-2-2</a>
          </td>
          <td>Proxy 間 TLS 中継（中継暗号化）</td>
          <td class="col-files">
            <a href="./images/stunnel-sclient.png" target="_blank"><code>stunnel-sclient.png</code></a>,
            <a href="./images/stunnel-log.png" target="_blank"><code>stunnel-log.png</code></a>
          </td>
        </tr>
        <tr>
          <td class="col-chapter"><a href="./#4-3-ネットワーク前提条件">4-3. WSL2 mirrored</a></td>
          <td class="col-id">
            <a href="#p4-3-1">P4-3-1</a>, <a href="#p4-3-2">P4-3-2</a>
          </td>
          <td>同一 L2 前提での Kerberos / DNS / WPAD 成立</td>
          <td class="col-files">
            <a href="./images/wsl2-mirrored-ip.png" target="_blank"><code>wsl2-mirrored-ip.png</code></a>,
            <a href="./images/ad-domain-join.png" target="_blank"><code>ad-domain-join.png</code></a>
          </td>
        </tr>
        <tr>
          <td class="col-chapter"><a href="./#5-1-プロキシ間通信の暗号化が必要だと理解するまで">5-1. stunnel導入経緯</a></td>
          <td class="col-id">
            <a href="#p5-1-1">P5-1-1</a>（任意）
          </td>
          <td>中継暗号化が必要となった理由整理</td>
          <td class="col-files">
            <a href="./images/no-stunnel-note.png" target="_blank"><code>no-stunnel-note.png</code></a>
          </td>
        </tr>
        <tr>
          <td class="col-chapter"><a href="./#5-2-多段-sslbump-が成立しない理由の理解">5-2. 多段SSLBump失敗</a></td>
          <td class="col-id">
            <a href="#p5-2-1">P5-2-1</a>（任意）
          </td>
          <td>復号制約の理解と設計修正</td>
          <td class="col-files">
            <a href="./images/double-bump-error.png" target="_blank"><code>double-bump-error.png</code></a>
          </td>
        </tr>
        <tr>
          <td class="col-chapter"><a href="./#5-3-認証基盤ad--ldap--kerberosの切り分け">5-3. 認証</a></td>
          <td class="col-id">
            <a href="#p5-3-1">P5-3-1</a>〜<a href="#p5-3-3">P5-3-3</a><br>
            （<a href="#p5-3-1">P5-3-1</a>, <a href="#p5-3-2">P5-3-2</a>, <a href="#p5-3-3">P5-3-3</a>）
          </td>
          <td>認証フロー・失敗レイヤの追跡</td>
          <td class="col-files">
            <a href="./images/kerberos-flow.png" target="_blank"><code>kerberos-flow.png</code></a>,
            <a href="./images/ldapwhoami.png" target="_blank"><code>ldapwhoami.png</code></a>,
            <a href="./images/ldapsearch-testuser1.png" target="_blank"><code>ldapsearch-testuser1.png</code></a>,
            <a href="./images/proxy-auth-log.png" target="_blank"><code>proxy-auth-log.png</code></a>
          </td>
        </tr>
        <tr>
          <td class="col-chapter"><a href="./#6-ログ監視observability">6. ログ・監視</a></td>
          <td class="col-id">
            <a href="#p6-1">P6-1</a>〜<a href="#p6-3">P6-3</a><br>
            （<a href="#p6-1">P6-1</a>, <a href="#p6-2">P6-2</a>, <a href="#p6-3">P6-3</a>）
          </td>
          <td>可視化・追跡（ログ／メトリクス）</td>
          <td class="col-files">
            <a href="./images/graylog-search.png" target="_blank"><code>graylog-search.png</code></a>,
            <a href="./images/grafana-proxy-deny.png" target="_blank"><code>grafana-proxy-deny.png</code></a>,
            <a href="./images/zabbix-hosts.png" target="_blank"><code>zabbix-hosts.png</code></a>
          </td>
        </tr>
        <tr>
          <td class="col-chapter"><a href="./#7-自動化運用性再現性の確保">7. 自動化</a></td>
          <td class="col-id">
            <a href="#p7-1">P7-1</a>, <a href="#p7-2">P7-2</a>
          </td>
          <td>再現性ある起動・検証・復旧</td>
          <td class="col-files">
            <a href="./images/dnsmasq-pac-healthcheck.png" target="_blank"><code>dnsmasq-pac-healthcheck.png</code></a>,
            <a href="./images/healthcheck-output.png" target="_blank"><code>healthcheck-output.png</code></a>
          </td>
        </tr>
        <tr>
          <td class="col-chapter"><a href="./#8-学習成果本プロジェクトを通じて得られた理解">8. 学習成果</a></td>
          <td class="col-id">
            <a href="#p8-1">P8-1</a>
          </td>
          <td>想定外事象の分析と言語化</td>
          <td class="col-files">
            <a href="./images/wpad-shortname-issue.png" target="_blank"><code>wpad-shortname-issue.png</code></a>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</div>

<p class="note"><em>
※ 5章の一部（P5-1-1 / P5-2-1）は再現により環境へ影響を与える可能性があるため、当時のログ・メモが存在する場合のみ掲載しています。
</em></p>

---

## 2. 共通前提

- 前提：WSL2上で docker compose が起動済み（主要コンテナが Up / healthy）
- 前提：Windowsクライアントは AD参加済み、WPAD/PAC を自動取得できる状態
- 証跡：画像は images/ に格納し、index.md から参照する

---

## P1：プロジェクト概要「全体が動いている」証拠（index.md 1章）

### 主張
多段プロキシ／認証／ログ／監視が **統合された状態で稼働**しており、  
起動確認・状態確認をスクリプトにより **再現可能な形で実施できる**ことを示す。
---

### 撮るもの
- ヘルスチェック結果（カテゴリ別に `[OK]` が揃っている状態）
- コンテナ稼働一覧（proxy1〜3、ldap、pac、graylog、loki、grafana、zabbix など）
---

### 手順

#### P1-1. Health Check 出力
<a id="p1-1"></a>
**実行**

    cd /home/login00/multiproxy
    ./scripts/multiproxy_health_all.sh

**確認内容**
- Proxy / Auth / Logging / Monitoring など  
  各カテゴリが `[OK]` となっていること
  
**証跡**
- `healthcheck-output.png`
---

#### P1-2. コンテナ一覧（docker compose ps）
<a id="p1-2"></a>
**実行**

    cd /home/login00/multiproxy
    docker compose ps

**確認内容**
- 主要コンテナが `Up`  
- 可能なものは `healthy` と表示されていること

**証跡**
- `docker-ps.png`

> 補足：  
> 掲載している「カテゴリ別一覧」は、  
> `docker compose ps --format json` の出力を jq / awk で整形し、  
> 稼働状況を視覚的に把握しやすくしたもの。

<details>

  <summary>（参考）json出力をカテゴリ別に整形して表示するコマンド（bash）</summary>

  <p style="margin: .5rem 0 0;">
    ※ <code>docker compose ps</code> の表示は、<code>--format json</code> を <code>jq/awk</code> で整形しています。
  </p>
  <pre style="margin-top:.6rem; padding:.8rem; overflow-x:auto; border:1px solid rgba(127,127,127,.25); border-radius:10px;">
　　<code class="language-bash">cd /home/login00/multiproxy
　　docker compose ps --format json \
　　| jq -r '
  　　def s($v): ($v // "" | tostring);
  　　def is_agent($x):
    　　(s($x) | test("(^|[-_])agent($|[-_])") or test("sidecar") or test("opensearch-agent"));　　
  　　def ports_short($r):
    　　if ($r.Publishers? and ($r.Publishers|length)>0) then
      　　($r.Publishers
        　　| map("\(.PublishedPort)->\(.TargetPort)/\(.Protocol)")
        　　| join(", "))
    　　elif ($r.Ports? and ($r.Ports|length)>0) then
      　　s($r.Ports)
    　　else "-" end;　　
  　　def cat($svc):
    　　if s($svc) | test("^proxy[123]$") then "Proxy"
    　　elif s($svc) | test("stunnel") then "Tunnel"
    　　elif s($svc) | test("^(icap|clamav)") then "Content-Scan"
    　　elif s($svc) | test("^(ldap|phpldapadmin)") then "Auth"
    　　elif s($svc) | test("^(pac|pac-renderer)") then "DNS-Routing"
    　　elif s($svc) | test("^(promtail|loki|graylog|opensearch|mongo|grafana|portainer)") then 　　"Logging"
    　　elif s($svc) | test("^zabbix") then "Monitoring"
    　　else "Logging" end;　　
  　　def ord($c):
    　　if $c=="Proxy" then 1
    　　elif $c=="Tunnel" then 2
    　　elif $c=="Content-Scan" then 3
    　　elif $c=="Auth" then 4
    　　elif $c=="DNS-Routing" then 5
    　　elif $c=="Logging" then 6
    　　elif $c=="Monitoring" then 7
    　　else 99 end;　　
  　　. as $r
  　　| ($r.Service|s(.)) as $svc
  　　| ($r.Name|s(.)) as $name
  　　| (cat($svc)) as $cat
  　　| select(is_agent($svc) | not)   # ← agent 行はここで除外
  　　| [ ord($cat),
　　      $cat,
      　　$svc,
      　　$name,
      　　s($r.State),
      　　s($r.Health),
      　　(s($r.CreatedAt)[0:16]),
      　　ports_short($r),
      　　(s($r.Image) | split(":")[0])
    　　]
  　　| @tsv
　　' \
　　| sort -t$'\t' -k1,1n -k3,3 \
　　| awk -F'\t' '
　　BEGIN{
  　　W_CAT=14; W_SVC=18; W_CONT=24; W_STATE=10; W_HEALTH=10; W_CREATED=16; W_PORTS=26;　　
  　　　　　　　　printf "%-*s | %-*s | %-*s | %-*s | %-*s | %-*s | %-*s | %s\n",
    　　　　W_CAT,"CATEGORY", W_SVC,"SERVICE", W_CONT,"CONTAINER",
    　　W_STATE,"STATE", W_HEALTH,"HEALTH", W_CREATED,"CREATED",
    　　W_PORTS,"PORTS", "IMAGE";　　
  　　printf "%s\n", 　　"--------------------------------------------------------------------------------------------　　-------------------------------------------";
  　　prev="";
　　}　　
　　function cut(s,w){
  　　if (length(s)<=w) return s;
  　　if (w<=3) return substr(s,1,w);
  　　return substr(s,1,w-3)"...";
　　}　　
　　{
  　　cat=$2; svc=$3; cont=$4; st=$5; hl=$6; cr=$7; pt=$8; img=$9;　　
  　　if (prev!="" && cat!=prev) print "";　　
  　　cat_disp = (cat==prev ? "" : cat);　
  　　printf "%-*s | %-*s | %-*s | %-*s | %-*s | %-*s | %-*s | %s\n",
    　　W_CAT,cat_disp,
    　　W_SVC,cut(svc,W_SVC),
    　　W_CONT,cut(cont,W_CONT),
    　　W_STATE,st,
    　　W_HEALTH,hl,
    　　W_CREATED,cr,
    　　W_PORTS,cut(pt,W_PORTS),
    　　img;　　
  　　prev=cat;
　　}
　　'
　</code>
  </pre>
</details>

### 証跡（比較表示）
以下、上記で取得した 2 枚を比較できるように表示する。

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

<p style="text-align:center; font-size:.9em; opacity:.8;">
※ 画像をクリックすると原寸で確認できます
</p>

---

## P2：アーキテクチャ／技術スタック（index.md 2章）

### 主張
設計したアーキテクチャおよび技術スタックが、  
**実際に稼働しているコンテナ構成・ソフトウェア構成と一致している**ことを示す。

---

### 撮るもの
- アーキテクチャ図（論理構成）
- 稼働中コンテナ一覧との対応関係
- 実行環境から取得した主要 OSS のバージョン一覧
- 代表的な OSS の個別バージョン確認結果

---

### 手順

#### P2-1. アーキテクチャ図と稼働コンポーネントの一致
<a id="p2-1"></a>

**目的**
- 設計したアーキテクチャ図と、  
  実際に起動しているコンテナ群が **1:1 で対応している**ことを確認する。

**実施内容**
1. アーキテクチャ図を作成  
   → `architecture.png`

2. コンテナ一覧から対応箇所を確認  
   → `docker-ps-arch-match.png`

### 証跡（比較表示）
以下、上記で取得した２枚を比較できるように表示する。

<div style="text-align:center; margin: 1.2em 0;">
  <a href="./images/P2-1：証跡_比較.png" target="_blank">
    <img
      src="./images/P2-1：証跡_比較.png"
      alt="P2-1 証跡比較（アーキテクチャ図 / 稼働コンテナ対応）"
      style="width:100%; max-width:1200px; height:auto;
             border-radius:12px;
             box-shadow:0 6px 18px rgba(0,0,0,.15);
             cursor: zoom-in;"
    >
  </a>
</div>

<p style="text-align:center; font-size:.9em; opacity:.8;">
※ 画像をクリックすると原寸で確認できます
</p>

---

### P2-2. 技術スタック（ソフトウェアバージョン）の確認
<a id="p2-2"></a>

**目的**
- Docker イメージタグではなく、  
  **実行中コンテナ内の実バイナリから取得したバージョン**を把握していることを示す。
---

#### P2-2-1. バージョン一覧（全体）
<a id="p2-2-1"></a>

**実行**
    cd /home/login00/multiproxy
    ./scripts/show-all-versions.sh

**確認内容**
- Proxy / 認証 / ログ / 監視 など主要 OSS の  
  実行中ソフトウェアバージョンが一覧で表示されること

**証跡**
- `version-matrix.png`

> ※ 本一覧は Docker イメージタグではなく、  
> 各コンテナ内で実行されているソフトウェアの実バージョンを取得している。

---

#### P2-2-2. 代表例（Squid）
<a id="p2-2-2"></a>

**実行**

    docker exec -it proxy1 squid -v

**確認内容**
- Squid のバージョン（例：5.7）が表示されること

**証跡**
- `squid-version.png`
---

### 証跡（比較表示）
以下、上記で取得した２枚を比較できるように表示する。

<div style="text-align:center; margin: 1.2em 0;">
  <a href="./images/P2-2：証跡_比較.png" target="_blank">
    <img
      src="./images/P2-2：証跡_比較.png"
      alt="P2-2 証跡比較（バージョン取得スクリプト / 個別コマンド）"
      style="width:100%; max-width:1200px; height:auto;
             border-radius:12px;
             box-shadow:0 6px 18px rgba(0,0,0,.15);
             cursor: zoom-in;"
    >
  </a>
</div>

<p style="text-align:center; font-size:.9em; opacity:.8;">
※ 画像をクリックすると原寸で確認できます
</p>

---

## P3：PAC / WPAD による経路制御（通常 vs DIRECT）（index.md 3章）

### 主張
クライアントが取得する PAC ファイルにより、  
**通信先ドメインに応じて Proxy1 / Proxy2 / DIRECT が正しく制御されている**ことを示す。

本章では、  
- クライアントが取得する PAC（wpad.dat）の内容  
- PAC が制御する「クライアント出口」と、  
  Squid が担う「内部中継」の責務分離  

が **設計どおりであること**を確認する。

---

### 撮るもの
- ブラウザから取得した PAC ファイル（wpad.dat）の内容
- PROXY / DIRECT の分岐定義が確認できる箇所（同一画面内）
- PAC による分岐点を示した通信経路図

---

### P3-1. PAC の内容確認
<a id="p3-1"></a>

**実施内容**

クライアント端末のブラウザで、PAC ファイルを直接取得する。

    http://wpad.ad.lan:8080/wpad.dat

---

**確認内容**

取得した PAC ファイル内に、以下の記述が存在することを確認する。

- `PROXY proxy1.ad.lan:3128`  
  （通常のクライアント入口）

- `PROXY proxy2.ad.lan:3131`  
  （Proxy1 を経由しない *DIRECT 相当* の入口）

- `DIRECT`  
  （社内通信・管理系 UI・例外通信の直通）

> 補足  
> `proxy2.ad.lan:3129` は **Proxy1 → Proxy2 の内部中継**として  
> Squid の `cache_peer` で使用するポートである。  
>  
> PAC は「クライアント → 最初の出口」を制御する仕組みのため、  
> **通常は PAC 側で 3129 を指定する必要はない。**

---

**証跡**

- ブラウザで表示された `wpad.dat` の内容（全体）
- 上記 `PROXY / DIRECT` の文字列が確認できる箇所  
  （同一画面内で確認できれば可）

→ `wpad-dat.png`

---

### P3-2. 通信経路図による分岐点の確認
<a id="p3-2"></a>

**目的**

PAC によって制御される範囲と、  
Squid / stunnel により形成される内部中継経路を分離して可視化し、  
**設計上の責務分担が明確であること**を示す。

**確認内容**

- PAC が制御するのは  
  **Client → Proxy1 / Proxy2 / DIRECT の入口選択のみ**であること
- Proxy1 → Proxy2（3129）以降は  
  Squid の `cache_peer` により自動中継されること

**証跡**

- PAC による分岐点を明示した通信経路図

→ `pac-routing-diagram.png`

---

### 証跡（表示）

<div style="text-align:center; margin: 1.2em 0;">
  <a href="./images/P3-1：証跡_比較.png" target="_blank">
    <img
      src="./images/P3-1：証跡_比較.png"
      alt="P3 通信経路図（PAC による分岐点）"
      style="width:100%; max-width:1200px; height:auto;
             border-radius:12px;
             box-shadow:0 6px 18px rgba(0,0,0,.15);
             cursor: zoom-in;"
    >
  </a>
</div>

<p style="text-align:center; font-size:.9em; opacity:.8;">
※ 画像をクリックすると原寸で確認できます
</p>

---

### 補足説明（お客様向け）

- PAC ファイルは **WPAD によりクライアントへ自動配布**
- クライアント側で個別のプロキシ設定は不要
- 通信先ドメインに応じて以下を自動判定
  - 社内系 / 管理系通信：DIRECT
  - 通常業務通信：Proxy1 → Proxy2（内部中継）
  - 一部例外通信：Proxy2 直通（Proxy1 skip）

本構成により、  
**入口制御（PAC）と内部中継（Squid）を分離した、実運用を想定した設計**であることを示している。

---
### （参考）通信経路_ポート表示（折りたたみ表示）
<details> 
　<summary>
　　<strong>通信経路・ポート詳細図（PACの責務 / Squidの責務を明示）</strong>
　</summary>
図A：全体概要
<pre>
  [ Client / Browser ]
          |
          | ① PAC により出口を選択
          |    - Proxy1
          |    - Proxy2(DIRECT相当)
          |    - DIRECT
          v
  +------------------------+
  | Proxy1 (Squid)         |
  | - 認証 / SSLBump       |
  | - ログ集約             |
  +------------------------+
          |
          | ② Proxy間通信（TLS）
          v
  +------------------------+
  | Proxy2 (Squid)         |
  | - 経路分岐             |
  | - ICAP / 制御          |
  +------------------------+
          |
          | ③ Proxy間通信（TLS）
          v
  +------------------------+
  | Proxy3 (Squid)         |
  | - 最終出口             |
  | - 送信制御 / ログ      |
  +------------------------+
</pre>

> 補足（重要）
> - PAC が制御するのは **Client → 最初の出口（Proxy / DIRECT）**までです。
> - Proxy1→Proxy2→Proxy3 の中継は Squid（cache_peer）側の責務です。

図B：通信経路・ポート詳細（LISTEN / SEND）
<pre>
  [ Client ]
    |
    | HTTP / HTTPS
    | （PAC により出口を選択）
    v
  +--------------------------------------------------+
  | Proxy1 (Squid)                                   |
  | LISTEN : 3128                                    |
  | ROLE   : 認証 / SSLBump / ログ集約               |
  | LOG    : /var/log/squid/access.log               |
  +--------------------------------------------------+
          |
          | cache_peer parent → proxy1-stunnel:13128
          v
  +--------------------------------------------------+
  | proxy1-stunnel (1→2 / Client)                    |
  | LISTEN : 13128 (ローカル待受)                     |
  | SEND   : proxy2-stunnel:4431 (TLS)               |
  | LOG    : stunnel12-client.log                    |
  +--------------------------------------------------+
          |
          | TLS : 4431
          v
  +--------------------------------------------------+
  | proxy2-stunnel (1→2 / Server)                    |
  | LISTEN : 4431 (TLS受信)                           |
  | SEND   : proxy2:3129 (HTTP)                      |
  | LOG    : stunnel12-server.log                    |
  +--------------------------------------------------+
          |
          | HTTP : 3129（内部中継）
          v
  +---------------------------------------------------------------+
  | Proxy2 (Squid)                                                 |
  | LISTEN : 3129 (Proxy1→Proxy2 通常経路)                         |
  |          3131 (Client→Proxy2 DIRECT相当)                      |
  | ROLE   : 経路制御 / ICAP                                      |
  | LOG    : access_3129.log / access_3131.log                    |
  +---------------------------------------------------------------+
          |
          | cache_peer parent → proxy2-3-stunnel:23129 / 23131
          v
  +---------------------------------------------------------------+
  | proxy2-3-stunnel (2→3 / Client)                                |
  | LISTEN : 23129 (通常) / 23131 (DIRECT相当)                     |
  | SEND   : proxy3-stunnel:4433 / 4434 (TLS)                     |
  | LOG    : stunnel23-client.log                                  |
  +---------------------------------------------------------------+
          |
          | TLS : 4433 / 4434
          v
  +---------------------------------------------------------------+
  | proxy3-stunnel (3側 / Server)                                  |
  | LISTEN : 4433 (通常) / 4434 (DIRECT相当)                       |
  | SEND   : proxy3:3130 / 3132 (HTTP)                             |
  | LOG    : stunnel23-server.log                                  |
  +---------------------------------------------------------------+
          |
          | HTTP : 3130 / 3132
          v
  +---------------------------------------------------------------+
  | Proxy3 (Squid)                                                  |
  | LISTEN : 3130 (通常経路)                                        |
  |          3132 (DIRECT相当)                                     |
  | ROLE   : 最終出口                                              |
  | LOG    : access_main.log / access_direct.log                   |
  +---------------------------------------------------------------+
</pre>

経路定義（ポートフォリオ明記用）
① 通常経路（Proxy1 経由・標準）
Client
  → Proxy1:3128
  → proxy1-stunnel:13128
  → proxy2-stunnel:4431
  → Proxy2:3129
  → proxy2-3-stunnel:23129
  → proxy3-stunnel:4433
  → Proxy3:3130

② DIRECT 相当経路（Proxy1 skip）
Client
  → Proxy2:3131
  → proxy2-3-stunnel:23131
  → proxy3-stunnel:4434
  → Proxy3:3132
</details>

### （参考）PAC ファイル内容（折りたたみ表示）
<details> 
  <summary>
    <strong>PAC ファイル（wpad.dat.tmpl）全文を表示</strong>
  </summary>

※ 実運用では、pac-renderer コンテナの起動（再起動）時に  
※ entrypoint により envsubst が実行され、wpad.dat が自動生成されます。  
※ 設定変更（.env 更新）後は pac-renderer の再起動により内容が反映されます。  
※ 読みやすさのため、ここではテンプレート全文を折りたたんで掲載します。

<pre>
  /* pac/wpad.dat.tmpl
   * ============================================================
   * Multiproxy WPAD / PAC（Kerberos + SSL-Bump 構成向け）
   * ============================================================
   *
   * ■このファイルは何か？
   *   - クライアント（ブラウザ）が「どの通信をどのプロキシへ流すか」を判断するための PAC ファイルです。
   *   - 本プロジェクトでは WPAD で自動配布し、ブラウザが wpad.dat を参照して経路を決定します。
   *
   * ■生成方法（重要）
   *   - 本ファイルは “テンプレート” です。
   *   - pac コンテナの entrypoint で envsubst により変数を展開し、
   *     /usr/share/nginx/html/wpad.dat として生成され、HTTP で配布されます。
   *
   * ■PAC が制御できる範囲
   *   - PAC が制御できるのは「クライアント → 最初の出口（Proxy / DIRECT）」のみです。
   *   - Proxy1→Proxy2 の内部中継（例：proxy2:3129）は Squid の cache_peer が担い、PAC の責務外です。
   *
   * ■本 PAC の狙い（誰が見ても分かる要点）
   *   1) ローカル（社内/管理UI）は DIRECT（プロキシを経由しない）
   *   2) 例外（銀行/行政など）は DIRECT
   *   3) 非業務（YouTube/Netflix）は DIRECT（例：帯域/運用方針のデモ）
   *   4) 一部ドメインは Proxy1 を強制（検査・認証・ログ集約の適用）
   *   5) 一部ドメインは Proxy2:3131 に振り分け（Proxy1 をスキップする “DIRECT相当” 経路）
   *   6) それ以外は Proxy1 →（フェイルオーバ）→ Proxy2 → DIRECT
   *
   * ============================================================
   *  .env から展開される主な変数（例）
   * ============================================================
   *
   * 【基本プロキシ設定（クライアントが指定する出口）】
   *   PAC_PROXY1_ADDR           例: proxy1.ad.lan:3128   （通常の入口）
   *   PAC_PROXY2_ADDR           例: proxy2.ad.lan:3129   （※注意：通常は内部中継。PACで使う必要はない）
   *   PAC_PROXY2_DIRECT_ADDR    例: proxy2.ad.lan:3131   （Proxy1 を経由しない “DIRECT相当”）
   *   PAC_FO_CHAIN              例: "PROXY proxy2.ad.lan:3129; DIRECT" など
   *
   * 【DIRECT 対象（社内/ローカル）】
   *   PAC_LOCAL_DOMAINS         例: .ad.lan,.lan,.local
   *   PAC_LOCAL_UI_HOSTS        例: localhost,wpad,graylog.ad.lan,zabbix.ad.lan
   *   PAC_LAN_NETS              例: 192.168.11.0/255.255.255.0
   *
   * 【例外 DIRECT（銀行/行政など）】
   *   PAC_BYPASS_SUFFIXES
   *
   * 【非業務 → DIRECT】
   *   PAC_NONBUS_SUFFIXES
   *
   * 【Proxy1 強制（検査・認証・ログ集約）】
   *   PAC_FORCE_PROXY1_SUFFIXES
   *
   * 【Proxy2 のみ（Proxy1 skip / DIRECT相当）】
   *   PAC_PROXY2_ONLY_SUFFIXES
   *
   * ============================================================
   *  1. ユーティリティ関数群
   * ============================================================
   */
  
  function listFromEnv(csv) {
    if (!csv) return [];
    return (csv + "").split(",").map(function(s){ return (s || "").trim(); }).filter(function(s){ return s.length; });
  }
  
  function listLanNetsFromEnv(spec) {
    if (!spec) return [];
    return (spec + "").split(";").map(function(s){ return (s || "").trim(); }).filter(function(s){ return s; }).map(function(s){
      var p = s.split("/");
      if (p.length !== 2) return null;
      return [p[0].trim(), p[1].trim()];
    }).filter(function(x){ return x; });
  }
  
  function isIPv4Literal(s) {
    return /^\d{1,3}(\.\d{1,3}){3}$/.test(s);
  }
    
  function validIPv4(s) {
    return isIPv4Literal(s);
  }
  
  function safeIsInNet(host, net, mask) {
    if (!validIPv4(net) || !validIPv4(mask)) return false;
    return isInNet(host, net, mask);
  }
  
  function inAnyLan(host, nets) {
    for (var i = 0; i < nets.length; i++) {
      if (safeIsInNet(host, nets[i][0], nets[i][1])) return true;
    }
    return false;
  }
  
  function matchPattern(host, pattern) {
    if (!pattern) return false;
    var p = (pattern + "").trim();
    if (!p) return false;
  
    // host:port → host（PAC の比較は host で行う）
    var c = p.indexOf(":");
    if (c > -1) p = p.substring(0, c);
  
    // IP リテラル（単一ホスト扱い）
    if (isIPv4Literal(p)) return safeIsInNet(host, p, "255.255.255.255");
  
    // 完全一致: "=host.example"
    if (p.charAt(0) === "=") return host === p.substring(1);
  
    // 前方一致: "^prefix"
    if (p.charAt(0) === "^") return host.indexOf(p.substring(1)) === 0;
  
    // サフィックス: ".example.com"
    if (p.charAt(0) === ".") return dnsDomainIs(host, p);
  
    // その他ワイルドカード: "*.example.com" 等
    return shExpMatch(host, p);
  }
  
  function anyMatch(host, list) {
    for (var i = 0; i < list.length; i++) {
      if (matchPattern(host, list[i])) return true;
    }
    return false;
  }
  
  /* ============================================================
   *  2. envsubst による展開対象（テンプレート変数）
   * ============================================================
   *
   * ※ここは envsubst で置換される前提のため、実運用では .env の値が入ります。
   * ※下記は “説明用デフォルト値” を含めています（空ならデフォルト補完）。
   */
  
  // --- env 値（文字列として展開される） ---
  var PROXY1_ADDR          = "proxy1.ad.lan:3128";   // 通常入口
  var PROXY2_ADDR          = "proxy2.ad.lan:3129";   // フェイルオーバ用（※PACで使う場合のみ）
  var PROXY2_DIRECT_ADDR   = "proxy2.ad.lan:3131";   // Proxy1 skip（DIRECT相当）
  var FO_CHAIN             = "PROXY proxy2.ad.lan:3129; DIRECT";
  
  // DIRECT / 例外 / ルーティング条件（CSV）
  var RAW_LOCAL_DOMAINS      = ".ad.lan,.lan,.local";
  var RAW_LOCAL_UI_HOSTS     = "localhost,127.0.0.1,graylog.ad.lan,zabbix.ad.lan,portainer.ad.lan,wpad,wpad.ad.lan,proxy1.ad.lan,proxy2.ad.lan";
  var RAW_BYPASS_SUFFIXES    = ".examplebank.co.jp,.gov.example.jp,.paypay.ne.jp,.rakuten-bank.co.jp";
  var RAW_NONBUS_SUFFIXES    = ".youtube.com,.youtu.be,.netflix.com";
  var RAW_P2_ONLY_SUFFIXES   = "wikipedia.org,.wikipedia.org";
  var RAW_FORCE_P1_SUFFIXES  = ".google.com,.yahoo.co.jp,.microsoft.com";
  var RAW_LAN_NETS           = "";
  
  // --- JS 側でデフォルト補完（空の場合にのみ適用） ---
  if (!PROXY1_ADDR) PROXY1_ADDR = "proxy1.ad.lan:3128";
  
  /*
   * 注意：
   * - PROXY2_ADDR は “フェイルオーバ連鎖” 用の予備として扱います。
   * - 本プロジェクトの通常設計では「Proxy1→Proxy2(3129)」は Squid の cache_peer が担います。
   *   そのため、PAC で PROXY2_ADDR を使うのは「Proxy1 が死んだ場合にクライアントが直接逃がす」など
   *   “デモ/要件” がある場合に限定するのが安全です。
   */
  if (!PROXY2_ADDR) PROXY2_ADDR = "proxy2.ad.lan:3129";
  
  // DIRECT相当用が未指定なら通常用にフォールバック（後方互換）
  if (!PROXY2_DIRECT_ADDR) PROXY2_DIRECT_ADDR = PROXY2_ADDR;
  
  // FO_CHAIN 未指定なら「Proxy2（予備）→DIRECT」
  if (!FO_CHAIN) FO_CHAIN = "PROXY " + PROXY2_ADDR + "; DIRECT";
  
  var LOCAL_DOMAINS = listFromEnv(RAW_LOCAL_DOMAINS);
  if (LOCAL_DOMAINS.length === 0) LOCAL_DOMAINS = [".ad.lan", ".lan", ".local"];
  
  var LOCAL_UI_HOSTS = listFromEnv(RAW_LOCAL_UI_HOSTS);
  if (LOCAL_UI_HOSTS.length === 0) {
  LOCAL_UI_HOSTS = ["localhost", "127.0.0.1", "wpad", "wpad.ad.lan", "graylog.ad.lan", "zabbix.ad.lan", "proxy1.ad.lan", "proxy2.ad.lan"];
  }
  
  var BYPASS_SUFFIXES = listFromEnv(RAW_BYPASS_SUFFIXES);
  if (BYPASS_SUFFIXES.length === 0) BYPASS_SUFFIXES = [".examplebank.co.jp", ".gov.example.jp"];
  
  var NONBUS_SUFFIXES = listFromEnv(RAW_NONBUS_SUFFIXES);
  if (NONBUS_SUFFIXES.length === 0) NONBUS_SUFFIXES = [".youtube.com", ".googlevideo.com", ".ytimg.com", ".netflix.com"];

  var PROXY2_ONLY_SUFFIXES = listFromEnv(RAW_P2_ONLY_SUFFIXES);
  var FORCE_P1_SUFFIXES    = listFromEnv(RAW_FORCE_P1_SUFFIXES);
  
  var LAN_NETS = listLanNetsFromEnv(RAW_LAN_NETS);
  if (LAN_NETS.length === 0) LAN_NETS = listLanNetsFromEnv("192.168.11.0/255.255.255.0");
  
  /* ============================================================
   *  3. PAC 本体（経路決定）
   * ============================================================
   *
   * 返す文字列の例：
   *   "DIRECT"
   *   "PROXY proxy1.ad.lan:3128; DIRECT"
   *   "PROXY proxy1.ad.lan:3128; PROXY proxy2.ad.lan:3129; DIRECT"
   */
  
  function FindProxyForURL(url, host) {
  
    // 0) ローカル / 社内は DIRECT（管理UIや社内DNS解決に強い）
    if (isPlainHostName(host)) return "DIRECT";
    if (inAnyLan(host, LAN_NETS)) return "DIRECT";
    if (anyMatch(host, LOCAL_DOMAINS)) return "DIRECT";
    if (anyMatch(host, LOCAL_UI_HOSTS)) return "DIRECT";
  
    // 1) 例外 DIRECT（銀行/行政など：復号・検査を避ける運用方針を想定）
    if (anyMatch(host, BYPASS_SUFFIXES)) return "DIRECT";
  
    // 2) 非業務系（YouTube / Netflix など：デモとして明示的に DIRECT）
    if (anyMatch(host, NONBUS_SUFFIXES)) return "DIRECT";
  
    // 3) Proxy1 強制（検査・認証・ログ集約を必ず通す）
    if (anyMatch(host, FORCE_P1_SUFFIXES)) {
    return "PROXY " + PROXY1_ADDR + "; DIRECT";
    }  
  
    // 4) Proxy2 のみに振り分け（Proxy1 skip / DIRECT相当用ポート）
    if (anyMatch(host, PROXY2_ONLY_SUFFIXES)) {
    return "PROXY " + PROXY2_DIRECT_ADDR + "; DIRECT";
    }
  
    // 5) デフォルト：Proxy1 → フェイルオーバ連鎖 → DIRECT
    //    ※ Proxy1→Proxy2 の通常中継は Squid(cache_peer) 側で実施する設計
    var p1 = "PROXY " + PROXY1_ADDR;
    var fo = (FO_CHAIN + "").trim();
  
    if (fo.length > 0) return p1 + "; " + fo;
  
    // FO_CHAIN が空の場合の保険（最小構成）
    if (PROXY2_ADDR) return p1 + "; PROXY " + PROXY2_ADDR + "; DIRECT";
  
    return p1 + "; DIRECT";
  }
</pre>
</details>
---

#### P3-2. 経路差のログ確認（NORMAL vs P2-DIRECT）
<a id="p3-2"></a>

### 目的
PAC により選択された通信経路の違いが、  
各 Proxy のログ出力差として明確に確認できることを示す。

- **NORMAL**：Proxy1 → Proxy2 → Proxy3  
- **P2-DIRECT**：Proxy2 → Proxy3（Proxy1 skip）  
- **ROUTER-DIRECT（参考）**：Proxy を一切経由しない  

---

### 事前準備（クライアント側アクセス）

以下はクライアント端末（Browser / curl）での打鍵例。  
※ PAC が適用されている前提。

---

#### NORMAL 対象サイト（例：www.google.com）

実行

```
https://www.google.com

```

---

#### P2-DIRECT 対象サイト（例：wikipedia.org）

実行する。

```
https://www.wikipedia.org
```

---

#### ROUTER-DIRECT（完全直通・PAC 無効の確認用）

※ 一時的にクライアントで「プロキシなし」にするか、  
※ 明示的に DIRECT 指定で確認する場合。

```
https://www.netflix.com

```

> 補足  
> ROUTER-DIRECT は PAC の責務外であり、  
> 「Proxy を一切経由しない場合の参考比較」として扱う。

---

### ログ確認手順（Proxy 側）

---

#### NORMAL（例：google.com）

```
docker compose exec -T proxy1 sh -lc \
"grep -aRInE 'google\.com' /var/log/squid/access*.log 2>/dev/null | tail -n 3"

docker compose exec -T proxy2 sh -lc \
"grep -aRInE 'google\.com' /var/log/squid/access*.log 2>/dev/null | tail -n 3"

docker compose exec -T proxy3 sh -lc \
"grep -aRInE 'google\.com' /var/log/squid/access*.log 2>/dev/null | tail -n 3"
---

#### P2-DIRECT（例：wikipedia.org）

```
docker compose exec -T proxy1 sh -lc \
"grep -aRInE 'wikipedia(\.org)?' /var/log/squid/access*.log 2>/dev/null | tail -n 3 || true"

docker compose exec -T proxy2 sh -lc \
"grep -aRInE 'wikipedia(\.org)?' /var/log/squid/access*.log 2>/dev/null | tail -n 3"

docker compose exec -T proxy3 sh -lc \
"grep -aRInE 'wikipedia(\.org)?' /var/log/squid/access*.log 2>/dev/null | tail -n 3"
```

---

#### ROUTER-DIRECT（参考：Netflix / Proxy 非経由）
```
# Netflix 本体ドメインだけを厳密チェック

```
docker compose exec -T proxy1 sh -lc \
'grep -aRInE "\"(GET|POST|CONNECT)[[:space:]]+https://(www\.netflix\.com|[^/ ]*\.nflx(video|so|img|ext)\.net)" \
 /var/log/squid/access*.log 2>/dev/null | tail -n 5 || true'

docker compose exec -T proxy2 sh -lc \
'grep -aRInE "(GET|POST|CONNECT)[[:space:]]+https://(www\.netflix\.com|[^/ ]*\.nflx(video|so|img|ext)\.net)" \
 /var/log/squid/access*.log 2>/dev/null | tail -n 5 || true'

docker compose exec -T proxy3 sh -lc \
'grep -aRInE "(GET|POST|CONNECT)[[:space:]]+https://(www\.netflix\.com|[^/ ]*\.nflx(video|so|img|ext)\.net)" \
 /var/log/squid/access*.log 2>/dev/null | tail -n 5 || true'

```

※ いずれの Proxy ログにも該当通信が出ないことを確認する。

---

### 判定基準（ポートフォリオ明記用）

**NORMAL**

- proxy1（3128）  
- proxy2（3129）  
- proxy3（3130）  

→ 全段にログが出る。

---

**P2-DIRECT**

- proxy1：ログなし  
- proxy2（3131）  
- proxy3（3132）  

→ Proxy1 を完全にスキップしている。

---

**ROUTER-DIRECT（参考）**

- proxy1 / proxy2 / proxy3：すべてログなし  

→ PAC / Proxy を一切経由していない。

---

### 証跡（スクリーンショット）

- 同一時間帯で取得したログ差分が分かる画面  
  - NORMAL：proxy1 / proxy2 / proxy3 のログ  
  - P2-DIRECT：proxy2 / proxy3 のみログ  

※ 1 枚に収まらない場合は左右比較でも可。

**証跡**
- `pac-flow-normal-vs-direct.png`

---

### 補足説明（お客様向け）

本検証により、PAC による経路制御が  
ログという客観的証跡で確認できることを示している。

特に P2-DIRECT は、  
**「Proxy1 を経由しない設計意図どおりの通信」**であることを  
明確に証明している。

ROUTER-DIRECT は、  
PAC / Proxy が介在しない場合の比較用参考として記載する。

**証跡**
- `pac-flow-normal-vs-direct.png`

---

## P4：SSLBump / stunnel / WSL2 mirrored（index.md 4章）

## P4-1：SSLBump の適用範囲（経路別）（index.md 4-1）

**主張（index.md 4-1 の裏付け）：**

- **経路①（通常）**  
  - Proxy1 が SSLBump（HTTPS 復号）を実施  
  - Proxy2 / Proxy3 は中継・出口を担当

- **経路②（DIRECT）**  
  - Proxy2 が SSLBump（HTTPS 復号）を実施  
  - Proxy1 は経由しない

---

#### 撮るもの

- クライアント側の証明書表示（internal CA）
- Proxy1 / Proxy2 のログ差分（復号ポイントの違い）

---

#### 手順

#### P4-1-1. HTTPS アクセス & 証明書
<a id="p4-1-1"></a>

- Windows クライアントで HTTPS サイト（例：`https://www.google.com`）にアクセス
- ブラウザの証明書表示を開き、  
  **発行者が Multiproxy Root CA になっている画面**をスクリーンショット  
  → `sslbump-cert.png`

---

#### P4-1-2. 経路①（通常）で Proxy1 が復号しているログ
<a id="p4-1-2"></a>

WSL2 で実行（例：www.google.com）：

    docker compose exec -T proxy1 sh -lc '
    LOGS="$(ls -1 /var/log/squid/access*.log 2>/dev/null || true)"
    [ -n "$LOGS" ] || { echo "[NG] no access*.log under /var/log/squid"; exit 0; }
    
    # Google系のHTTPS(= https://) かつ bump=bump を拾って「復号が行われた」証拠にする
    PAT="(https://[^ ]*google\.com/|CONNECT ([^ ]*\.)?google\.com:443)"
    
    echo "### Proxy1 SSLBump evidence (google.com) : latest 3"
    grep -aRInE "$PAT" $LOGS 2>/dev/null \
      | grep -a "bump=bump" \
      | tail -n 3
    '

- `ssl_bump` を伴うログが Proxy1 に出ている部分をスクリーンショット  
  → `sslbump-proxy1-log.png`

---

#### P4-1-3. 経路②（DIRECT）で Proxy2 が復号しているログ
<a id="p4-1-3"></a>

DIRECT 対象サイト（例：wikipedia.org）へアクセス後、  
Proxy2 の 3131 ポート側ログを確認：

    docker compose exec -T proxy2 sh -lc '
    LOG=/var/log/squid/access_3131.log
    PAT="wikipedia\.org"
    
    echo "### file: $LOG"
    grep -aE "$PAT" "$LOG" 2>/dev/null | grep -a "bump=bump" | tail -n 3
    '

- Proxy2（3131）で復号が行われていることが分かるログをスクリーンショット  
  → `sslbump-proxy2-log.png`

---

## P4-2：stunnel によるプロキシ間 TLS（中継暗号化）（index.md 4-2）

**主張（index.md 4-2 の裏付け）：**  
商用プロキシでは機器内機能として提供される  
**「プロキシ間通信の暗号化（中継 TLS）」** を、  
Squid 単体では実現できないため、**stunnel によって分離実装**している。

---

#### P4-2-1. openssl s_client による TLS 確認
<a id="p4-2-1"></a>

Proxy2 → Proxy3 間の通信が、  
**stunnel によって TLS で暗号化されていること**を確認する。

- TLS セッションが確立していること（`CONNECTION ESTABLISHED`）
- TLSv1.3 がネゴシエートされていること
- Proxy3 側 stunnel がサーバ証明書を提示していること
- Squid の通信とは独立した TLS レイヤであること

---

#### 確認結果（スクリーンショット）

- `CONNECTION ESTABLISHED` が表示されている  
- `Protocol version: TLSv1.3` が確認できる  
- `Ciphersuite: TLS_AES_256_GCM_SHA384` が使用されている  
- `Peer certificate: CN = proxy3-stunnel` が表示されている  

上記より、**Proxy2–Proxy3 間の中継通信は stunnel により TLS で暗号化されている**ことが分かる。

→ `stunnel-sclient.png`

---

#### 設計上の意味

- Squid で復号された後の **プロキシ間通信を平文のまま流さない**
- 商用プロキシ製品における  
  **「内部中継通信の暗号化」** を OSS で再現
- SSLBump（復号）と 中継TLS（再暗号化）を  
  **役割分離した構成** として実装

本構成により、  
**「クライアント向け HTTPS 復号」と「プロキシ間通信の秘匿」** を  
独立した責務として管理できている。

---

## P4-2：WSL2 mirrored mode & AD/DC 連携

**主張：**  
Kerberos / WPAD / DNS の前提条件となる  
**「クライアント・WSL2・AD/DC・DNS が同一 L2 セグメントに存在する構成」**を満たし、  
商用ネットワークと同等の条件で検証できている。

本構成では、WSL2 を **Mirrored Networking Mode** で動作させることで、  
NAT 配下ではなく **実 LAN に直接参加する構成**を実現している。

---

#### P4-2-1. IP アドレス比較（同一セグメント）
<a id="p4-3-1"></a>

以下の 4 つを並べてスクリーンショットを取得する：

- Windows クライアント：  
      ipconfig | Select-String -Pattern "IPv4|サブネット|デフォルト ゲートウェイ"
- WSL2（Ubuntu）：  
      ip -4 -o addr show scope global
- AD/DC VM：  
      ip -4 -o addr show scope global
- dnsmasq VM： 
      ip -4 -o addr show scope global


- すべてが同一セグメント（例：`192.168.11.0/24`）であることが分かる画面  
  → `wsl2-mirrored-ip.png`

#### 確認できること

- Windows クライアントと WSL2 が **同一 IPv4 アドレス体系**に存在している  
- AD/DC・dnsmasq も同一 L2 セグメント上に配置されている  
- WSL2 が NAT 越しではなく、**ブロードキャスト／Kerberos 前提の通信が成立する配置**になっている  

---

#### P4-3-2. ドメイン参加および Kerberos 認証の証拠
<a id="p4-3-2"></a>

本環境は **RDP 接続のみ可能な検証構成**のため、  
物理ログイン画面は取得していない。

代わりに、ログイン後の状態から  
**Active Directory ドメイン参加および Kerberos 認証が実際に利用されていること**を確認する。

以下の情報を抜粋してスクリーンショット取得：

- `whoami` の実行結果が  
  **`ad\testuser1`** となっていること

- `systeminfo` の出力にて  
  - **OS 構成：メンバー ワークステーション**
  - **ドメイン：ad.lan**
  - **ログオン サーバー：\\DC1**  
  が確認できること

- `klist` の出力にて  
  - **`HTTP/proxy1.ad.lan`**
  - **`HTTP/proxy2.ad.lan`**  
  に対する Kerberos チケットが発行されていること

これにより、  
**Windows クライアントが Samba AD/DC に参加し、  
Kerberos 認証を用いて Proxy（Squid）と連携通信している**ことが分かる。

→ `ad-domain-kerberos.png`
---

#### 設計上の意味

- Kerberos（SPN 解決・チケット発行）が **NAT 非依存で成立**
- WPAD / DNS / Proxy 認証の前提条件を **実ネットワーク同等条件で検証**
- 商用プロキシ環境で一般的な  
**「クライアントと同一セグメントに配置される認証・中継基盤」**を再現

本項により、本構成が  
**机上構成ではなく、実運用を想定したネットワーク前提で構築されている**ことを示している。

---

---

## P5：学習ログ（導入経緯／失敗／認証切り分け）（index.md 5章）

---

## P5-1：stunnel が必要だと理解するに至った過程（index.md 5-1）

**主張：**  
商用プロキシでは暗黙的に提供される  
**「プロキシ間（中継区間）の暗号化」** を、  
OSS（Squid）の制約と脅威モデルから分解し、  
**自ら再設計できることを示す。**

---

#### P5-1-1. 設計判断の整理（思考ログ）
<a id="p5-1-1"></a>

- 当初は「Proxy1 で SSLBump すれば十分」と考えていた
- しかし以下の点に気づいた：
  - Proxy1–Proxy2–Proxy3 間の通信は **復号後は平文**
  - Squid 単体では「中継区間のみを再暗号化」する手段がない
- 商用プロキシ製品では内部的に実装されている  
  **「機器間通信の TLS 化」** が OSS では欠落していると判断

このため、

- **HTTPS 復号（SSLBump）**
- **プロキシ間通信の暗号化（中継 TLS）**

を **責務分離** し、  
中継暗号化を **stunnel により外付け実装** する設計に至った。

※ 本節は設計判断の説明を目的とし、  
　**意図的に壊す再現テストは行わない。**

---

### P5-2：多段 SSLBump が失敗した理由（=復号は1回のみ）

**主張：**  
HTTPS 復号（SSLBump）は **1 通信につき 1 回のみ**である。  
経路①（Proxy1 → Proxy2:3129）で Proxy2 側でも bump を適用すると、**同一セッションに対する二重復号**となり TLS が破綻する。  
このため、経路①では **Proxy2:3129 を splice 固定**し、復号点を分離する設計へ改善した。

---

#### P5-2-1. 二重SSLBump（Proxy1→Proxy2:3129）による失敗の観測
<a id="p5-2-1"></a>

**目的**  
- 「多段 SSLBump（同一セッションへの二重復号）は成立しない」ことを、  
  設計（復号点の分離）と、実ログ（503 / TLS エラー）で説明できる状態にする。

**対象**  
- 経路①：Client → Proxy1:3128 → Proxy2:3129 → Proxy3:3130  
- 観測サイト：`https://www.google.com`

---

#### 撮るもの（成果物）
- Proxy2 access_3129.log：chain 側で **bump が走った痕跡（bump=bump 等）**  
  → `double-sslbump-proxy2-access3129.png`
- Proxy1 access log：`CONNECT 200 → GET https://www.google.com 503` が確認できる画面  
  → `double-sslbump-proxy1-access-google-503.png`
- （任意）ブラウザ側エラー（TLS/証明書系）  
  → `double-sslbump-browser-error.png`
- （補足）Proxy1 側でも同一通信が `bump=bump` で復号済みであることを確認  
  → `double-sslbump-proxy1-bump.png`

---

#### 証跡（スクリーンショット）
- Proxy2 access_3129.log：`bump=bump`（chain 側で bump が走った痕跡）  
  ![double-sslbump-proxy2-access3129](assets/evidence/double-sslbump-proxy2-access3129.png)

- Proxy1 access log：`CONNECT 200 → GET https://www.google.com 503`  
  ![double-sslbump-proxy1-access-google-503](assets/evidence/double-sslbump-proxy1-access-google-503.png)

- （任意）ブラウザエラー：`X509_V_ERR_SELF_SIGNED_CERT_IN_CHAIN`  
  ![double-sslbump-browser-error](assets/evidence/double-sslbump-browser-error.png)

- （補足）Proxy1 側でも同一通信が `bump=bump` で復号済みであることを確認  
  ![proxy1-bump-evidence](assets/evidence/double-sslbump-proxy1-bump.png)

<details>
<summary><strong>検証手順（コマンド含む・折りたたみ）</strong></summary>

- Proxy2 の `61-ssl-bump.conf` にて `p2_chain_3129` を一時的に `stare/bump` に変更
- 反映：`squid -k parse` / `squid -k reconfigure`
- Windows/Ubuntu から `https://www.google.com` にアクセス
- ログ採取：Proxy1/Proxy2 の access/cache log

👉 詳細なコマンド全文：`evidence/p5-2.md`

</details>

---

## P5-3：AD / LDAP / Kerberos のフロー可視化（index.md 5-3）

**主張：**  
認証をブラックボックスにせず、  
**CLI とログを用いて段階的に切り分け・確認できる。**

---

#### P5-3-1. ldapwhoami（LDAP 疎通確認）
<a id="p5-3-1"></a>

    docker compose exec -T ldap sh -lc '
    BIND_PW="$(cat /run/secrets/bind_pw)"
    ldapwhoami -x -H ldap://ldap:389 \
      -D "CN=LDAP Proxy,OU=Service Accounts,DC=ad,DC=lan" \
      -w "$BIND_PW"
    '

- `dn: CN=LDAP Proxy,...` が表示される画面  
  → `ldapwhoami.png`

---

#### P5-3-2. testuser1 の検索
<a id="p5-3-2"></a>

    docker compose exec -T ldap sh -lc '
    BIND_PW="$(cat /run/secrets/bind_pw)"
    ldapsearch -LLL -x -H ldap://ldap:389 \
      -D "CN=LDAP Proxy,OU=Service Accounts,DC=ad,DC=lan" \
      -w "$BIND_PW" \
      -b "OU=TestUsers,DC=ad,DC=lan" \
      "(sAMAccountName=testuser1)" dn sAMAccountName cn
    '

- `dn: CN=testuser1,...` が確認できる画面  
  → `ldapsearch-testuser1.png`

---

#### P5-3-3. Proxy ログに認証ユーザが出ていることの確認
<a id="p5-3-3"></a>

    docker compose exec -T proxy1 sh -lc \
      "grep -a 'testuser1@AD.LAN' /var/log/squid/access.log | tail -n 10"

- Kerberos 認証されたユーザ名付きアクセスログが確認できる画面  
  → `proxy-auth-log.png`

---

#### P5-3-1. ldapwhoami（LDAP 疎通確認）
<a id="p5-3-1"></a>

    docker exec -it ldap sh -lc '
    BIND_PW="$(cat /run/secrets/bind_pw)"
    ldapwhoami -x -H ldap://ldap:389 \
      -D "CN=LDAP Proxy,OU=Service Accounts,DC=ad,DC=lan" \
      -w "$BIND_PW"
    '

- `dn: CN=LDAP Proxy,...` が表示される画面  
  → `ldapwhoami.png`

---

#### P5-3-2. testuser1 の検索
<a id="p5-3-2"></a>

    docker exec -it ldap sh -lc '
    BIND_PW="$(cat /run/secrets/bind_pw)"
    ldapsearch -LLL -x -H ldap://ldap:389 \
      -D "CN=LDAP Proxy,OU=Service Accounts,DC=ad,DC=lan" \
      -w "$BIND_PW" \
      -b "OU=TestUsers,DC=ad,DC=lan" \
      "(sAMAccountName=testuser1)" dn sAMAccountName cn
    '

- `dn: CN=testuser1,...` が確認できる画面  
  → `ldapsearch-testuser1.png`

---

#### P5-3-3. Proxy ログに認証ユーザが出ていることの確認
<a id="p5-3-3"></a>

    docker exec -it proxy1 sh -lc \
      "grep -a 'testuser1@AD.LAN' /var/log/squid/access.log | tail -n 10"

- Kerberos 認証されたユーザ名付きアクセスログが確認できる画面  
  → `proxy-auth-log.png`
---

## P6：ログ・監視（Observability）（index.md 6章）

**主張：**  
ログ（Graylog / Loki）と監視（Zabbix）を組み合わせ、  
**障害の兆候検知と原因特定に当たりを付けられる**。

---

<a id="p6-1"></a>
#### P6-1. Graylog 検索

- 検索例：  
      TCP_DENIED AND facebook.com
- ログ一覧とフィールドが表示されている画面  
  → `graylog-search.png`

---
<a id="p6-2"></a>
#### P6-2. Grafana（Loki）

- Explore → LogQL 例：

    {container="proxy1"} |= "TCP_DENIED"

- 時系列グラフ＋ログ一覧の画面  
  → `grafana-proxy-deny.png`

---
<a id="p6-3"></a>
#### P6-3. Zabbix

- Hosts 一覧がすべて Green  
  → `zabbix-hosts.png`
- proxy1 のグラフ（トラフィック / deny カウント）  
  → `zabbix-proxy1-graph.png`

---

## P7：自動化（再現性）（index.md 7章）

**主張：**  
手作業ではなく、  
**スクリプトで「壊しても戻せる・確認できる」状態**を作っている。

---

<a id="p7-1"></a>
#### P7-1. dnsmasq + PAC ヘルスチェック

    cd /home/login00/multiproxy/scripts
    ./dnsmasq_pac_healthcheck.sh

- WPAD 解決 / PAC GET / PROXY 行が確認できる画面  
  → `dnsmasq-pac-healthcheck.png`

---
<a id="p7-2"></a>
#### P7-2. 全体ヘルスチェック（再掲）

- <a href="#p1-1">P1-1</a> の結果を再利用可  
  → `healthcheck-output.png`

---

## P8：想定外動作の切り分け（WPAD 短縮名問題）（index.md 8章）

**主張：**  
「なんか変」で終わらせず、  
**仕様と挙動を調べて原因を言語化できる**。

---

<a id="p8-1"></a>
#### P8-1. WPAD 短縮名問題の証跡（Windows クライアント）

PowerShell：

    nslookup wpad
    ping wpad
    Test-NetConnection wpad -Port 8080

ブラウザ：

    http://wpad.ad.lan:8080/wpad.dat

- 4 つの結果を 1 枚にまとめてスクリーンショット  
  → `wpad-shortname-issue.png`

---

#### 期待する「絵」の意味付け（面接向け）

- 短縮名（`wpad`）は NG
- FQDN（`wpad.ad.lan`）は OK

これを  
**「Windows の WPAD 保護仕様」** として説明できることで、  
単なるトラブル対応ではなく  
**設計前提を理解した上での切り分け能力**が伝わる。
