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
    <table>
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
          <td><a href="./#1-プロジェクト概要summary">1. プロジェクト概要</a></td>
          <td>
            <a href="#p1-1">P1-1</a>, <a href="#p1-2">P1-2</a>
          </td>
          <td>全体稼働（ヘルスチェック／全コンテナ稼働）</td>
          <td><code>healthcheck-output.png</code>, <code>docker-ps.png</code></td>
        </tr>
        <tr>
          <td><a href="./#2-システムアーキテクチャ全体像">2. システムアーキテクチャ</a></td>
          <td>
            <a href="#p2-1">P2-1</a>
          </td>
          <td>構成図と実稼働コンポーネントの一致</td>
          <td><code>architecture.png</code>, <code>docker-ps-arch-match.png</code></td>
        </tr>
        <tr>
          <td><a href="./#構成要素すべて-oss">2. 構成要素（技術スタック）</a></td>
          <td>
            <a href="#p2-2-1">P2-2-1</a>, <a href="#p2-2-2">P2-2-2</a>
          </td>
          <td>OSS 採用理由・役割整理（技術選定の妥当性）＋主要バージョン把握</td>
          <td><code>version-matrix.png</code>, <code>squid-version.png</code></td>
        </tr>
        <tr>
          <td><a href="./#3-通信経路と設計構成変更点">3. PAC / WPAD</a></td>
          <td>
            <a href="#p3-1">P3-1</a>, <a href="#p3-2">P3-2</a>
          </td>
          <td>PAC 取得・経路切替（通常／DIRECT）</td>
          <td><code>wpad-dat.png</code>, <code>pac-flow-normal-vs-direct.png</code></td>
        </tr>
        <tr>
          <td><a href="./#4-1-復号sslbump位置の整理">4-1. SSLBump</a></td>
          <td>
            <a href="#p4-1-1">P4-1-1</a>〜<a href="#p4-1-3">P4-1-3</a>
            （<a href="#p4-1-1">P4-1-1</a>, <a href="#p4-1-2">P4-1-2</a>, <a href="#p4-1-3">P4-1-3</a>）
          </td>
          <td>経路別の復号境界（通常／DIRECT）</td>
          <td><code>sslbump-cert.png</code>, <code>sslbump-proxy1-log.png</code>, <code>sslbump-proxy2-log.png</code></td>
        </tr>
        <tr>
          <td><a href="./#4-2-プロキシ間通信の暗号化">4-2. stunnel</a></td>
          <td>
            <a href="#p4-2-1">P4-2-1</a>, <a href="#p4-2-2">P4-2-2</a>
          </td>
          <td>Proxy 間 TLS 中継（中継暗号化）</td>
          <td><code>stunnel-sclient.png</code>, <code>stunnel-log.png</code></td>
        </tr>
        <tr>
          <td><a href="./#4-3-ネットワーク前提条件">4-3. WSL2 mirrored</a></td>
          <td>
            <a href="#p4-3-1">P4-3-1</a>, <a href="#p4-3-2">P4-3-2</a>
          </td>
          <td>同一 L2 前提での Kerberos / DNS / WPAD 成立</td>
          <td><code>wsl2-mirrored-ip.png</code>, <code>ad-domain-join.png</code></td>
        </tr>
        <tr>
          <td><a href="./#5-1-プロキシ間通信の暗号化が必要だと理解するまで">5-1. stunnel導入経緯</a></td>
          <td>
            <a href="#p5-1-1">P5-1-1</a>（任意）
          </td>
          <td>中継暗号化が必要となった理由整理</td>
          <td><code>no-stunnel-note.png</code></td>
        </tr>
        <tr>
          <td><a href="./#5-2-多段-sslbump-が成立しない理由の理解">5-2. 多段SSLBump失敗</a></td>
          <td>
            <a href="#p5-2-1">P5-2-1</a>（任意）
          </td>
          <td>復号制約の理解と設計修正</td>
          <td><code>double-bump-error.png</code></td>
        </tr>
        <tr>
          <td><a href="./#5-3-認証基盤ad--ldap--kerberosの切り分け">5-3. 認証</a></td>
          <td>
            <a href="#p5-3-1">P5-3-1</a>〜<a href="#p5-3-3">P5-3-3</a>
            （<a href="#p5-3-1">P5-3-1</a>, <a href="#p5-3-2">P5-3-2</a>, <a href="#p5-3-3">P5-3-3</a>）
          </td>
          <td>認証フロー・失敗レイヤの追跡</td>
          <td><code>kerberos-flow.png</code>, <code>ldapwhoami.png</code>, <code>ldapsearch-testuser1.png</code>, <code>proxy-auth-log.png</code></td>
        </tr>
        <tr>
          <td><a href="./#6-ログ監視observability">6. ログ・監視</a></td>
          <td>
            <a href="#p6-1">P6-1</a>〜<a href="#p6-3">P6-3</a>
            （<a href="#p6-1">P6-1</a>, <a href="#p6-2">P6-2</a>, <a href="#p6-3">P6-3</a>）
          </td>
          <td>可視化・追跡（ログ／メトリクス）</td>
          <td><code>graylog-search.png</code>, <code>grafana-proxy-deny.png</code>, <code>zabbix-hosts.png</code></td>
        </tr>
        <tr>
          <td><a href="./#7-自動化運用性再現性の確保">7. 自動化</a></td>
          <td>
            <a href="#p7-1">P7-1</a>, <a href="#p7-2">P7-2</a>
          </td>
          <td>再現性ある起動・検証・復旧</td>
          <td><code>dnsmasq-pac-healthcheck.png</code>, <code>healthcheck-output.png</code></td>
        </tr>
        <tr>
          <td><a href="./#8-学習成果本プロジェクトを通じて得られた理解">8. 学習成果</a></td>
          <td>
            <a href="#p8-1">P8-1</a>
          </td>
          <td>想定外事象の分析と言語化</td>
          <td><code>wpad-shortname-issue.png</code></td>
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
多段プロキシ／認証／ログ／監視が **統合された状態で稼働**しており、復旧・確認もスクリプトで再現できる。

---

### 撮るもの
- ヘルスチェック結果（カテゴリ別に [OK] が揃う）
- コンテナ稼働一覧（proxy1〜3、ldap、pac、graylog、loki、grafana、zabbix など）

---

### 手順

<a id="p1-1"></a>
#### P1-1. Health Check 出力

WSL2 で実行：

    cd /home/login00/multiproxy
    ./scripts/multiproxy_health_all.sh

- 画面上で `[OK]` が一通り揃っていることを確認してスクリーンショットを取得  
  → `healthcheck-output.png`

---

<a id="p1-2"></a>
#### P1-2. コンテナ一覧（docker compose ps）

WSL2 で実行：

    cd /home/login00/multiproxy
    docker compose ps

- 主要コンテナが `Up`（可能なら `healthy`）になっている画面をスクリーンショット  
  → `docker-ps.png`

> 注：掲載している「カテゴリ表示の一覧」は、  
> `docker compose ps --format json` の出力を **jq/awk で整形**して読みやすくしたもの。

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
    elif s($svc) | test("^(promtail|loki|graylog|opensearch|mongo|grafana|portainer)") then "Logging"
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

  printf "%s\n", "---------------------------------------------------------------------------------------------------------------------------------------";
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
以下、上記で取得した２枚を比較できるように表示する。
（※ クリックで原寸表示）

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

### P2-1：アーキテクチャ図と稼働コンポーネントの一致

**主張：**  
設計したアーキテクチャと、実際に稼働しているコンポーネントが  
**1:1 で対応していることを示す。**

**撮るもの：**
- `architecture.png`
- `docker-ps.png` の対応箇所トリミング  
  → `docker-ps-arch-match.png`

---
<a id="p2-1"></a>
#### P2-1. アーキ図とコンテナ対応

<div style="text-align:center; margin: 1.2em 0;">
  <img
    src="./images/P2-1：証跡_比較.png"
    alt="P1 証跡比較（Health Check / Container 状態）"
    style="width:100%; height:auto; border-radius:12px; box-shadow:0 6px 18px rgba(0,0,0,.15);"
  >
</div>

---

### P2-2：技術スタック（バージョンと役割）の証拠

**主張：**  
OSS を「なんとなく使う」のではなく、  
**役割・制約・バージョンを把握した上で採用・構成している。**

---
<a id="p2-2-1"></a>
#### P2-2-1. バージョン表

WSL2 で実行：

    cd /home/login00/multiproxy
    ./scripts/show-all-versions.sh

- 画面上で ソフトウェアバージョン が一通り揃っていることを確認してスクリーンショットを取得  
  → `version-matrix.png`

  ※ 本一覧は Docker イメージタグではなく、
　各コンテナ内で実行されているソフトウェアの実バージョンを取得している。

---
<a id="p2-2-2"></a>
#### P2-2-2. 代表例（Squid）

WSL2 で実行：

    docker exec -it proxy1 squid -v

- Squid のバージョン（例：5.7）が表示されている画面をスクリーンショット  
  → `squid-version.png`

---

## P3：PAC / WPAD による経路制御（通常 vs DIRECT）（index.md 3章）

**主張：**  
商用環境で一般的な  
**「宛先・条件に応じたプロキシ経路切替」** を、PAC / WPAD で再現している。

---

<a id="p3-1"></a>
#### P3-1. PAC の内容確認

クライアントブラウザで以下にアクセス：

    http://wpad.ad.lan:8080/wpad.dat

- PAC 内に以下が記載されている箇所をスクリーンショット：
  - `PROXY proxy1.ad.lan:3128`
  - `PROXY proxy2.ad.lan:3129`
  - `PROXY proxy2.ad.lan:3131`
  - `DIRECT`

→ `wpad-dat.png`

---
<a id="p3-2"></a>
#### P3-2. 経路差のログ確認（NORMAL vs P2-DIRECT）

NORMAL 対象サイトと DIRECT 対象サイトへアクセス後、各 Proxy のログを比較する。

###### NORMAL（例：cloudflare.com）

    docker exec -it proxy1 bash -lc \
      "grep -a 'cloudflare.com' /var/log/squid/access.log | tail -n 10"

    docker exec -it proxy2 bash -lc \
      "grep -a 'cloudflare.com' /var/log/squid/access_3129.log | tail -n 10"

    docker exec -it proxy3 bash -lc \
      "grep -a 'cloudflare.com' /var/log/squid/access_main.log | tail -n 10"

###### P2-DIRECT（例：wikipedia.org）

    docker exec -it proxy1 bash -lc \
      "grep -a 'wikipedia.org' /var/log/squid/access.log | tail -n 10"

    docker exec -it proxy2 bash -lc \
      "grep -a 'wikipedia.org' /var/log/squid/access_3131.log | tail -n 10"

    docker exec -it proxy3 bash -lc \
      "grep -a 'wikipedia.org' /var/log/squid/access_direct.log | tail -n 10"

- NORMAL：`proxy1 / proxy2 / proxy3` すべてにログが出る  
- DIRECT：`proxy1` には出ず、`proxy2(3131)` と `proxy3(3132)` のみに出る  

この差が分かる画面をスクリーンショット  
→ `pac-flow-normal-vs-direct.png`

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
<a id="p4-1-1"></a>
#### P4-1-1. HTTPS アクセス & 証明書

- Windows クライアントで HTTPS サイト（例：`https://cloudflare.com`）にアクセス
- ブラウザの証明書表示を開き、  
  **発行者が internal CA になっている画面**をスクリーンショット  
  → `sslbump-cert.png`

---
<a id="p4-1-2"></a>
#### P4-1-2. 経路①（通常）で Proxy1 が復号しているログ

WSL2 で実行（例：cloudflare.com）：

    docker exec -it proxy1 bash -lc \
      "grep -a 'cloudflare.com' /var/log/squid/access.log | tail -n 20"

- `ssl_bump` を伴うログが Proxy1 に出ている部分をスクリーンショット  
  → `sslbump-proxy1-log.png`

---
<a id="p4-1-3"></a>
#### P4-1-3. 経路②（DIRECT）で Proxy2 が復号しているログ

DIRECT 対象サイト（例：wikipedia.org）へアクセス後、  
Proxy2 の 3131 ポート側ログを確認：

    docker exec -it proxy2 bash -lc \
      "grep -a 'wikipedia.org' /var/log/squid/access_3131.log | tail -n 20"

- Proxy2（3131）で復号が行われていることが分かるログをスクリーンショット  
  → `sslbump-proxy2-log.png`

---

## P4-2：stunnel によるプロキシ間 TLS（中継暗号化）（index.md 4-2）

**主張（index.md 4-2 の裏付け）：**  
商用プロキシでは機器内機能として提供される  
**「プロキシ間通信の暗号化（中継 TLS）」** を、  
Squid 単体では実現できないため、**stunnel によって分離実装**している。

---

<a id="p4-2-1"></a>
#### P4-2-1. openssl s_client による TLS 確認

WSL2 で実行：

    docker exec -it proxy2-3-stunnel sh -lc \
      'openssl s_client -connect proxy3-stunnel:4433 \
       -servername proxy3-stunnel \
       -brief </dev/null 2>&1 | head -n 15'

- `CONNECTION ESTABLISHED`
- `Protocol version: TLSv1.3` などが確認できる画面をスクリーンショット  
  → `stunnel-sclient.png`

---
<a id="p4-2-2"></a>
#### P4-2-2. stunnel ログの確認

WSL2 で実行：

    docker exec -it proxy1-stunnel sh -lc \
      "tail -n 80 /var/log/stunnel/stunnel.log"

- `SSL accepted` / `Connection established` 等が確認できる箇所をスクリーンショット  
  → `stunnel-log.png`

---

## P4-3：WSL2 mirrored mode & AD/DC 連携（index.md 4-3）

**主張：**  
Kerberos / WPAD / DNS の前提となる **「同一 L2 セグメント」** を満たし、  
実環境（商用ネットワーク）と同等の条件で検証できている。

---

<a id="p4-3-1"></a>
#### P4-3-1. IP アドレス比較（同一セグメント）

以下の 3 つを並べてスクリーンショット：

- Windows クライアント：  
      ipconfig
- WSL2（Ubuntu）：  
      ip a
- AD/DC VM：  
      ip a

- すべてが同一セグメント（例：`192.168.11.0/24`）であることが分かる画面  
  → `wsl2-mirrored-ip.png`

---
<a id="p4-3-2"></a>
#### P4-3-2. ドメイン参加の証拠

- Windows ログイン画面で  
  **AD ユーザ（例：`AD\testuser1`）が選択できる状態**をスクリーンショット  
  → `ad-domain-join.png`

---

## P5：学習ログ（導入経緯／失敗／認証切り分け）（index.md 5章）

## P5-1：stunnel が必要だと理解するに至った過程（任意）（index.md 5-1）

**主張：**  
商用プロキシでは暗黙的に提供される  
**「中継区間の暗号化」** を、OSS の制約と脅威モデルから分解し、  
自ら再設計できることを示す。

---

<a id="p5-1-1"></a>
#### P5-1-1. 当時の設計メモ／判断理由（再現はしない）

- 当時の設計メモや判断理由を index.md 5-1 から再掲
- **意図的に壊す再現テストは行わない**
  - 再現が必要な場合は別ブランチで実施
- テキスト主体の説明画像でも可

→ `no-stunnel-note.png`（メモ／設計判断のスクリーンショット）

---

## P5-2：多段 SSLBump が失敗したログ（任意）（index.md 5-2）

**主張：**  
**「HTTPS 復号は 1 通信につき 1 回のみ」** という制約を、  
実際の失敗を通じて理解し、  
**経路別に復号ポイントを分離する設計**へ落とし込んだ。

---

<a id="p5-2-1"></a>
#### P5-2-1. 当時保存していたエラーログ（再現不要）

- 当時保存していたエラーログ（再現不要）
  - 例：`TCP_MISS/503`、TLS handshake error 等
- 「proxy2 でも bump しようとして失敗した」ことが分かる内容

→ `double-bump-error.png`

※ 再現は必須ではなく、**当時のログ＋設計改善の説明**で十分。

---

## P5-3：AD / LDAP / Kerberos のフロー可視化（index.md 5-3）

**主張：**  
認証をブラックボックスにせず、  
**CLI とログを使って認証フローを段階的に切り分けられる**。

---

<a id="p5-3-1"></a>
#### P5-3-1. ldapwhoami（LDAP 疎通確認）

    docker exec -it ldap sh -lc '
    BIND_PW="$(cat /run/secrets/bind_pw)"
    ldapwhoami -x -H ldap://ldap:389 \
      -D "CN=LDAP Proxy,OU=Service Accounts,DC=ad,DC=lan" \
      -w "$BIND_PW"
    '

- `dn: CN=LDAP Proxy,...` が表示される画面をスクリーンショット  
  → `ldapwhoami.png`

---
<a id="p5-3-2"></a>
#### P5-3-2. testuser1 の検索

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
<a id="p5-3-3"></a>
#### P5-3-3. Proxy ログに認証ユーザが出ていることの確認

    docker exec -it proxy1 sh -lc \
      "grep -a 'testuser1@AD.LAN' /var/log/squid/access.log | tail -n 10"

- ユーザ名付きアクセスログが確認できる画面  
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
