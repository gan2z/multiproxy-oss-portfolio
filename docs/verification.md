---
title: Verification
layout: default
---

# Verification
（OSS Multi-Layer Proxy & Authentication System）

Version: 2025-01-15  
Author: gan2

---

## 0. このページの位置付け

このページは、index.md で示した設計思想・構成が  
**実際に動作していることを、スクリーンショットとログで裏付ける**ための検証ページです。

- 「設計どおりに動いているか」
- 「どこで・何が起きているかを説明できるか」

を **図とログ中心** に確認できる構成にしています。

<p style="margin:.6em 0 0 0;">
※ 再現性（構築・起動・検証・復旧の手順）は
<strong><a href="./automation.html">automation</a></strong> に集約しています。
本ページは <strong>動作証跡（スクリーンショット／ログ）</strong>に集中しています。
</p>

---

## 目次

- <a href="#highlights">要点（3分で把握）</a>
- <a href="#map">index.md の主張 ↔ 検証証跡（対応表）</a>
- <a href="#p1">1. 全体稼働と構成要素一致</a>
- <a href="#p2">2. アーキテクチャと構成対応</a>
- <a href="#p3">3. 経路制御（PAC）</a>
- <a href="#p4">4. 復号（SSLBump）と中継TLS（stunnel）</a>
- <a href="#p5">5. ログ基盤（Loki）での切り分け</a>
- <a href="#p6">6. 自動化と再現性</a>

---

<a id="highlights"></a>
## 要点（3分で把握）

1. **全コンポーネントが稼働し、構成図と 1:1 で対応付け可能**
2. **復号（SSLBump）と中継TLS（stunnel）を分離し、責務境界を明確化**
3. **経路制御（PAC）により、通信経路をログで判別可能**
4. **ログ基盤（Loki）により、通信事象を原因まで追跡可能**
5. **起動・確認・復旧を自動化し、環境差なく再現可能**

---

<a id="map"></a>
## index.md の主張 ↔ 検証証跡 対応（要点）

> 下表は「何を証明するための図か」を整理した一覧です。  
> **実際の証拠（図面）は、この表の直下にすべて掲載しています。**

<div class="table-compact">
  <div class="table-wrap">
    <table>
      <thead>
        <tr>
          <th>観点</th>
          <th>何を確認できるか</th>
          <th>対応する証跡（クリックで原寸表示）</th>
        </tr>
      </thead>
      <tbody>

        <tr>
          <td>全体稼働</td>
          <td>構成要素が欠けなく稼働している</td>
          <td>
            <a href="./images/P1-proof-all.png" target="_blank" rel="noopener"><code>P1-proof-all.png</code></a>
          </td>
        </tr>

        <tr>
          <td>アーキテクチャ</td>
          <td>設計（責務/経路）と実装（稼働要素）が対応している</td>
          <td>
            <a href="./images/P2-arch-compare.png" target="_blank" rel="noopener"><code>P2-arch-compare.png</code></a>
          </td>
        </tr>

        <tr>
          <td>経路制御（PAC）</td>
          <td>PAC により Proxy1 / Proxy2 / DIRECT が分岐し、ログで裏取りできる</td>
          <td>
            <a href="./images/pac-flow-normal-vs-direct.png" target="_blank" rel="noopener"><code>pac-flow-normal-vs-direct.png</code></a>
          </td>
        </tr>

        <tr>
          <td>復号 / 暗号化</td>
          <td>SSLBump と stunnel の責務分離（復号点とTLS境界が明確）</td>
          <td>
            <a href="./images/P4-proof-compare.png" target="_blank" rel="noopener"><code>P4-proof-compare.png</code></a>
          </td>
        </tr>

        <tr>
          <td>可観測性（Loki）</td>
          <td>経路と失敗点を時系列で追い、原因レイヤで説明できる</td>
          <td>
            <a href="./images/p6-observability-triage.png" target="_blank" rel="noopener"><code>p6-observability-triage.png</code></a>
          </td>
        </tr>

        <tr>
          <td>自動化</td>
          <td>環境クリア→起動→初期化→監視/ログ→ヘルス確認まで再現可能</td>
          <td>
            <a href="./images/all_in_one_overview.png" target="_blank" rel="noopener"><code>all_in_one_overview.png</code></a><br>
            <a href="./images/all_in_one_summary.png" target="_blank" rel="noopener"><code>all_in_one_summary.png</code></a><br>
            <a href="./images/healthcheck-output.png" target="_blank" rel="noopener"><code>healthcheck-output.png</code></a>
          </td>
        </tr>

      </tbody>
    </table>
  </div>
</div>

---

<a id="p1"></a>
## 1. 全体稼働と構成要素一致の確認

<figure style="margin: 1.2em auto; text-align:center;">
  <a href="./images/P1-proof-all.png" target="_blank" rel="noopener">
    <img
      src="./images/P1-proof-all.png"
      alt="全体稼働（healthcheck / docker ps）確認"
      loading="lazy"
      style="
        display:block;
        margin:0 auto;
        width:100%;
        max-width:1200px;
        height:auto;
        cursor:zoom-in;
        border:1px solid rgba(0,0,0,.12);
        border-radius:10px;
        box-shadow:0 6px 18px rgba(0,0,0,.10);
      "
    >
  </a>
  <figcaption style="margin-top:.6em; font-size:.92em; opacity:.85;">
    クリック/タップで原寸表示（別タブ）。
    <strong>構成要素（コンテナ/サービス）が欠けていない</strong>ことを確認しています。
  </figcaption>
</figure>

**確認できること**
- 全コンテナ（構成要素）が **running/healthy** で稼働している
- 監視・ログ・認証・DNS・Proxy/トンネル等の **横断要素も含めて**稼働している
- ここでの観測は「構成図そのもの」ではなく、**稼働チェック（inventory/health）**の証跡

<details>
  <summary><strong>構成要素（すべて OSS）※クリックで開く</strong></summary>

  <div class="table-compact" id="stack-table" style="margin-top:.8em;">
    <div class="table-wrap" style="overflow:auto;">
      <table>
        <thead>
          <tr>
            <th>区分</th>
            <th>役割・位置づけ</th>
            <th>採用技術</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td>プロキシ</td>
            <td>入口／分岐／出口の3段構成による通信制御</td>
            <td>Squid（3段、経路別ポート設計）</td>
          </tr>
          <tr>
            <td>中継暗号化</td>
            <td>Proxy 間通信の TLS 化（中継区間の暗号化）</td>
            <td>stunnel（Proxy1→2 / Proxy2→3）</td>
          </tr>
          <tr>
            <td>コンテンツ検査</td>
            <td>ウイルス・コンテンツ検査（横断機能）</td>
            <td>ICAP / ClamAV</td>
          </tr>
          <tr>
            <td>認証</td>
            <td>ユーザ認証・認可（SSO）</td>
            <td>OpenLDAP / Samba AD/DC / Kerberos</td>
          </tr>
          <tr>
            <td>DNS / 経路制御</td>
            <td>PAC 配布・名前解決</td>
            <td>dnsmasq（WPAD / Split DNS）</td>
          </tr>
          <tr>
            <td>ログ</td>
            <td>アクセス・通信ログの集中管理</td>
            <td>Promtail / Loki / Graylog / OpenSearch</td>
          </tr>
          <tr>
            <td>監視</td>
            <td>稼働・性能の可視化</td>
            <td>Zabbix（TLS-PSK + Sidecar）</td>
          </tr>
          <tr>
            <td>自動化</td>
            <td>起動・検証・復旧の再現性確保</td>
            <td>Bash</td>
          </tr>
          <tr>
            <td>実行環境</td>
            <td>検証用基盤</td>
            <td>Ubuntu 24.04（WSL2 mirrored mode）</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>

</details>

---

<a id="p2"></a>
## 2. アーキテクチャと構成対応

<figure style="margin: 1.2em auto; text-align:center;">
  <a href="./images/P2-arch-compare.png" target="_blank" rel="noopener">
    <img
      src="./images/P2-arch-compare.png"
      alt="アーキテクチャ図と稼働コンポーネントの対応"
      loading="lazy"
      style="
        display:block;
        margin:0 auto;
        width:100%;
        max-width:1200px;
        height:auto;
        cursor:zoom-in;
        border:1px solid rgba(0,0,0,.12);
        border-radius:10px;
        box-shadow:0 6px 18px rgba(0,0,0,.10);
      "
    >
  </a>
  <figcaption style="margin-top:.6em; font-size:.92em; opacity:.85;">
    設計（責務/経路）と、実装（起動している構成要素）が対応していることを確認します。
  </figcaption>
</figure>

**確認できること**
- 役割（入口／分岐／出口）が、実装（Proxy1/2/3・トンネル・検査・認証）に反映されている
- アーキテクチャ図（責務・通信経路）と、稼働コンポーネントが **欠けなく起動**している

---

<a id="p3"></a>
## 3. 経路制御（PAC）の検証

<p style="margin:.4em 0 1em 0;">
本章では、<strong>PAC による「出口選択（経路）」</strong>が
<strong>意図どおりに実通信へ反映されている</strong>ことを、
<strong>概要 → 設定 → 実通信ログ</strong>の順に確認します。
</p>

<hr>

### 3-0. PAC による経路制御の全体整理（概要）

<figure style="margin: 1.2em auto; text-align:center;">
  <a href="./images/pac-flow-normal-vs-direct.png" target="_blank" rel="noopener">
    <img
      src="./images/pac-flow-normal-vs-direct.png"
      alt="PACによる経路制御の全体概要"
      loading="lazy"
      style="
        display:block;
        margin:0 auto;
        width:100%;
        max-width:1200px;
        height:auto;
        cursor:zoom-in;
        border:1px solid rgba(0,0,0,.12);
        border-radius:10px;
        box-shadow:0 6px 18px rgba(0,0,0,.10);
      "
    >
  </a>
  <figcaption style="margin-top:.6em; font-size:.92em; opacity:.85;">
    PAC により <strong>Proxy1 / Proxy2（DIRECT相当） / DIRECT</strong> のいずれを出口として選択するかを整理した概要図。
  </figcaption>
</figure>

**ここで整理しているポイント**
- クライアントは PAC により「通信の出口（経路）」を選択する
- 通常通信は <strong>Proxy1 → Proxy2 → Proxy3</strong>
- 条件により <strong>Proxy2 直行（Proxy1 skip）</strong> / <strong>DIRECT（ルーター直通）</strong> に分岐する
- 以降は、この分岐が <strong>ログの有無</strong>で裏取りできることを示す

<hr>

### 3-1. クライアント側：PAC ファイルの有効化

<figure style="margin: 1.2em auto; text-align:center;">
  <a href="./images/pac-client-setting.png" target="_blank" rel="noopener">
    <img
      src="./images/pac-client-setting.png"
      alt="クライアントでのPAC設定"
      loading="lazy"
      style="
        display:block;
        margin:0 auto;
        width:100%;
        max-width:1100px;
        height:auto;
        cursor:zoom-in;
        border:1px solid rgba(0,0,0,.12);
        border-radius:10px;
        box-shadow:0 6px 18px rgba(0,0,0,.10);
      "
    >
  </a>
  <figcaption style="margin-top:.6em; font-size:.92em; opacity:.85;">
    クライアントに PAC（wpad.dat）を設定し、自動構成スクリプトが有効化されている状態。
  </figcaption>
</figure>

**確認できること**
- クライアントが PAC を取得・適用している
- 以降の通信は PAC 判定を経由する前提が成立している

<hr>

### 3-2. Proxy1 経由の通信（通常経路）

<figure style="margin: 1.2em auto; text-align:center;">
  <a href="./images/pac-proof-proxy1.png" target="_blank" rel="noopener">
    <img
      src="./images/pac-proof-proxy1.png"
      alt="Proxy1経由通信の確認"
      loading="lazy"
      style="
        display:block;
        margin:0 auto;
        width:100%;
        max-width:1100px;
        height:auto;
        cursor:zoom-in;
        border:1px solid rgba(0,0,0,.12);
        border-radius:10px;
        box-shadow:0 6px 18px rgba(0,0,0,.10);
      "
    >
  </a>
  <figcaption style="margin-top:.6em; font-size:.92em; opacity:.85;">
    Proxy1 → Proxy2 → Proxy3 の通常経路で通信していることを、Proxy1 のアクセスログで確認。
  </figcaption>
</figure>

**確認できること**
- PAC 判定により Proxy1 が選択されている（入口ログが残る）
- 通常経路が成立している

<hr>

### 3-3. Proxy2 経由の通信（Proxy1 skip / DIRECT 相当）

<figure style="margin: 1.2em auto; text-align:center;">
  <a href="./images/pac-proof-proxy2.png" target="_blank" rel="noopener">
    <img
      src="./images/pac-proof-proxy2.png"
      alt="Proxy2経由通信の確認"
      loading="lazy"
      style="
        display:block;
        margin:0 auto;
        width:100%;
        max-width:1100px;
        height:auto;
        cursor:zoom-in;
        border:1px solid rgba(0,0,0,.12);
        border-radius:10px;
        box-shadow:0 6px 18px rgba(0,0,0,.10);
      "
    >
  </a>
  <figcaption style="margin-top:.6em; font-size:.92em; opacity:.85;">
    Proxy1 を経由せず Proxy2 から通信していることを、Proxy1/Proxy2 のログ差分で確認。
  </figcaption>
</figure>

**確認できること**
- Proxy1 側にログが残らない（＝通っていない）
- Proxy2 側にのみログが残る（＝直行が成立）

<hr>

### 3-4. DIRECT（ルーター直通）通信

<figure style="margin: 1.2em auto; text-align:center;">
  <a href="./images/pac-proof-direct.png" target="_blank" rel="noopener">
    <img
      src="./images/pac-proof-direct.png"
      alt="DIRECT通信の確認"
      loading="lazy"
      style="
        display:block;
        margin:0 auto;
        width:100%;
        max-width:1100px;
        height:auto;
        cursor:zoom-in;
        border:1px solid rgba(0,0,0,.12);
        border-radius:10px;
        box-shadow:0 6px 18px rgba(0,0,0,.10);
      "
    >
  </a>
  <figcaption style="margin-top:.6em; font-size:.92em; opacity:.85;">
    Proxy1/Proxy2 のいずれにもログが残らず、ルーターへ直接通信していることを確認。
  </figcaption>
</figure>

**確認できること**
- PAC 判定により DIRECT が選択され、Proxy を完全にバイパスしている

<hr>

### 3-5. 検証サマリ（PAC 経路制御）

**本章で証明できたこと**
- PAC により <strong>通信経路を動的に切り替え</strong>できる
- 分岐（Proxy1 / Proxy2 / DIRECT）が <strong>実通信として成立</strong>している
- 判定は <strong>Proxy 側ログの有無</strong>で客観的に裏取りできる

---

<a id="p4"></a>
## 4. 復号（SSLBump）と中継 TLS（stunnel）

<p style="margin:.4em 0 1em 0;">
本章では、HTTPS 復号（SSLBump）の<strong>制約</strong>と<strong>失敗例</strong>を明示した上で、  
制約を踏まえて <strong>復号（SSLBump）と中継暗号化（stunnel）を分離</strong>した設計を確認します。
</p>

<hr>

### 4-0. 前提整理：SSLBump は「1 通信につき 1 回のみ」

<figure style="margin: 1.2em auto; text-align:center;">
  <a href="./images/P4-sslbump-overview.png" target="_blank" rel="noopener">
    <img
      src="./images/P4-sslbump-overview.png"
      alt="SSLBump と TLS 境界の整理"
      loading="lazy"
      style="
        display:block;
        margin:0 auto;
        width:100%;
        max-width:1100px;
        height:auto;
        cursor:zoom-in;
        border:1px solid rgba(0,0,0,.12);
        border-radius:10px;
        box-shadow:0 6px 18px rgba(0,0,0,.10);
      "
    >
  </a>
  <figcaption style="margin-top:.6em; font-size:.92em; opacity:.85;">
    SSLBump と stunnel の役割分離（復号点とTLS境界）を整理した概要図。
  </figcaption>
</figure>

**設計上の重要な前提（観測に基づく）**
- 同一 TLS セッションへの <strong>二重SSLBump</strong>は成立しない（通信が破綻する）
- 多段 Proxy では「どこで復号するか」を明確化する必要がある
- Proxy 間の暗号化は SSLBump ではなく <strong>stunnel（純粋なTLS）</strong>で担保する

<hr>

### 4-1. 失敗例：Proxy1 / Proxy2 の二重 SSLBump によるエラー（観測）

<figure style="margin: 1.2em auto; text-align:center;">
  <a href="./images/double-bump-error.png" target="_blank" rel="noopener">
    <img
      src="./images/double-bump-error.png"
      alt="二重SSLBumpによるブラウザエラー"
      loading="lazy"
      style="
        display:block;
        margin:0 auto;
        width:100%;
        max-width:1100px;
        height:auto;
        cursor:zoom-in;
        border:1px solid rgba(0,0,0,.12);
        border-radius:10px;
        box-shadow:0 6px 18px rgba(0,0,0,.10);
      "
    >
  </a>
</figure>

<figure style="margin: 1.2em auto; text-align:center;">
  <a href="./images/double-sslbump-proxy1-access-google-500.png" target="_blank" rel="noopener">
    <img
      src="./images/double-sslbump-proxy1-access-google-500.png"
      alt="Proxy1側（入口）のエラーログ"
      loading="lazy"
      style="
        display:block;
        margin:0 auto;
        width:100%;
        max-width:1100px;
        height:auto;
        cursor:zoom-in;
        border:1px solid rgba(0,0,0,.12);
        border-radius:10px;
        box-shadow:0 6px 18px rgba(0,0,0,.10);
      "
    >
  </a>
</figure>

<figure style="margin: 1.2em auto; text-align:center;">
  <a href="./images/double-sslbump-proxy2-access3129.png" target="_blank" rel="noopener">
    <img
      src="./images/double-sslbump-proxy2-access3129.png"
      alt="Proxy2側（3129）の観測ログ"
      loading="lazy"
      style="
        display:block;
        margin:0 auto;
        width:100%;
        max-width:1100px;
        height:auto;
        cursor:zoom-in;
        border:1px solid rgba(0,0,0,.12);
        border-radius:10px;
        box-shadow:0 6px 18px rgba(0,0,0,.10);
      "
    >
  </a>
</figure>

**ここで確認できること（面接官向け：短く）**
- 二重SSLBump時は、ブラウザ側にエラーが出る
- Proxy 側ログから、TLS/SSLBump 由来の失敗として追跡できる  
  （※エラー画面の “返答者” は入口の Proxy1 に見えるが、原因は “経路内のTLS/SSLBump整合” で起こり得る）

<hr>

### 4-2. 改善後の設計：復号と中継暗号化の分離

**最終設計方針**
- SSLBump（復号）は <strong>Proxy1 のみ</strong>
- Proxy 間通信は <strong>stunnel によるTLS</strong>で暗号化
- Proxy2 / Proxy3 は復号せず、<strong>復号済み通信を制御・中継</strong>

**得られる効果**
- SSLBump の制約（1通信1回）を遵守
- 復号点と TLS 境界が明確になり、運用時の切り分けが容易

<hr>

### 4-3. 検証サマリ（SSLBump / stunnel）

**本章で証明できたこと**
- SSLBump は <strong>1 通信 1 回</strong>の制約を持つ
- 二重SSLBumpは通信障害を引き起こす（観測できる）
- 復号（SSLBump）と中継暗号化（stunnel）の分離で安定化できる

---

<a id="p5"></a>
## 5. ログ基盤（Loki）を用いた切り分け

<p style="margin:.4em 0 1em 0;">
本章では、Loki（Promtail 収集）で <strong>Proxy1/2/3 の Squid ログ</strong>を横断検索し、
「どの経路を通り、どのレイヤで失敗したか」を<strong>時系列で切り分け</strong>できることを示します。<br>
基本は <strong>access.log＝経路（結果）</strong> / <strong>cache.log＝原因レイヤ（裏取り）</strong> です。
</p>

<hr>

<figure style="margin: 1.2em auto; text-align:center;">
  <a href="./images/p6-observability-triage.png" target="_blank" rel="noopener">
    <img
      src="./images/p6-observability-triage.png"
      alt="Lokiによる経路と失敗点の切り分け"
      loading="lazy"
      style="
        display:block;
        margin:0 auto;
        width:100%;
        max-width:1200px;
        height:auto;
        cursor:zoom-in;
        border:1px solid rgba(0,0,0,.12);
        border-radius:10px;
        box-shadow:0 6px 18px rgba(0,0,0,.10);
      "
    >
  </a>
  <figcaption style="margin-top:.6em; font-size:.92em; opacity:.85;">
    Proxy1/2/3 のログを横断し、経路と失敗点を時系列で追跡するイメージ（クリックで原寸表示）。
  </figcaption>
</figure>

**確認できること**
- 経路と失敗点を <strong>時系列で追跡</strong>できる
- HTTPコードの結果だけでなく、<strong>原因レイヤ（認証 / ACL / TLS / ICAP）</strong>で説明できる

<hr>

### 5-1. 収集対象（Promtail → Loki）

- Squid（Proxy1/2/3）：access.log / cache.log
- （任意）ICAP：c-icap / clamav logs
- （任意）stunnel：TLS中継ログ

<hr>

### 5-2. 実例（面接官向け：結論だけ）

- **Case A（入口で停止）**：Proxy1 で 407 → Proxy2/3 に到達していない  
- **Case B（出口側で停止）**：Proxy2:3131 で 403 → Proxy1 を経由していない（直行経路）

<details>
  <summary><strong>初学者向け：Grafana Explore（Loki）での見方 / LogQL / 具体手順（クリックで開く）</strong></summary>

  <hr>

  <h4>5-2-1. まずここから（画面操作：迷わない手順）</h4>

  <ol>
    <li>
      <strong>Grafana を開く</strong> → 左メニューの <strong>Explore</strong> をクリック
    </li>
    <li>
      画面上部のデータソースで <strong>Loki</strong> を選択
      （見当たらない場合は、右上の datasource を開いて候補から Loki を選ぶ）
    </li>
    <li>
      右上の <strong>時間範囲</strong> をテスト時刻の <strong>±2分</strong> に絞る  
      （例：date を実行した時刻を基準に「Last 5 minutes」→さらに狭める）
    </li>
    <li>
      下のクエリ欄で、まずは <strong>ラベル候補を確認</strong>：
      <ul>
        <li><strong>Label browser</strong>（または "Labels"）を開く</li>
        <li>よくあるラベル：<code>job</code>, <code>container</code>, <code>filename</code>, <code>instance</code></li>
      </ul>
    </li>
  </ol>

  <h4>5-2-2. 最初の一発（ラベルが分からない場合）</h4>

  <p style="margin:.3em 0 .8em 0;">
  まずは <code>{}</code> のまま全文検索で当たりを付けます（結果が出たらラベルに絞ります）。
  </p>

  <pre><code>{} |= " 407 " |= "google"
{} |= " 403 " |= "wikipedia"</code></pre>

  <p style="margin:.4em 0 0 0;">
  結果が出たら、ログ行に付いているラベル（job/container/filename等）を見て、次から絞り込みます。
  </p>

  <h4>5-2-3. access.log（結果）で追う：よく使うクエリ</h4>

  <pre><code>{} |= "access" |= " 407 "
{} |= "access" |= " 403 "
{} |= "access" |= " 502 "  # 上流到達/TLS中継が怪しいとき
{} |= "access" |= " 503 "</code></pre>

  <p style="margin:.4em 0 0 0;">
  ※ <code>access</code> は filename ラベルに入っている場合が多いです。入っていない場合は消してください。
  </p>

  <h4>5-2-4. cache.log（原因）で追う：よく使うクエリ</h4>

  <pre><code>{} |= "cache" |= "ERROR"
{} |= "cache" |= "WARNING"
{} |= "cache" |= "SslBump"
{} |= "cache" |= "DENIED"
{} |= "cache" |= "icap"</code></pre>

  <h4>5-2-5. Case A：入口（Proxy1）で 407 を観測</h4>

  <p style="margin:.3em 0 .8em 0;">
  <strong>狙い：</strong> Proxy1 だけに 407 が出ること（＝入口で停止）
  </p>

  <pre><code>{} |= "www.google.com" |= " 407 "</code></pre>

  <p style="margin:.4em 0 0 0;">
  その後、同じ条件で Proxy2/3 側のログが出ない（または薄い）ことを確認します。
  </p>

  <h4>5-2-6. Case B：出口側（Proxy2:3131）で 403 を観測</h4>

  <p style="margin:.3em 0 .8em 0;">
  <strong>狙い：</strong> Proxy2:3131 側に 403 が出て、Proxy1 には出ない（＝直行経路で停止）
  </p>

  <pre><code>{} |= "wikipedia" |= " 403 "</code></pre>

  <p style="margin:.4em 0 0 0;">
  次に cache.log 側で DENIED/ACL 系を拾って「原因レイヤ」を裏取りします。
  </p>

  <pre><code>{} |= "wikipedia" |= "DENIED"</code></pre>

  <hr>

  <h4>5-2-7. スクリーンショット（差し替え用のPNG）</h4>

  <ul>
    <li><code>images/p6-loki-caseA-proxy1-407.png</code>：Proxy1 で 407 を確認した Explore 画面</li>
    <li><code>images/p6-loki-caseA-proxy2-nohit.png</code>：Proxy2 側で NO HIT（同条件で出ない）を示す画面</li>
    <li><code>images/p6-loki-caseB-proxy2-403.png</code>：Proxy2 で 403 を確認した Explore 画面</li>
    <li><code>images/p6-loki-caseB-proxy1-nohit.png</code>：Proxy1 側で NO HIT を示す画面</li>
    <li><code>images/p6-loki-caseB-proxy2-cache-denied.png</code>：cache.log 側の DENIED を示す画面</li>
  </ul>

</details>

<hr>

### 5-3. 検証サマリ（Loki切り分け）

**本章で証明できたこと**
- 3段プロキシのログを Loki で横断検索できる
- 「経路（Proxy1経由 / Proxy2直行）」をログで裏取りできる
- 失敗を <strong>原因レイヤ</strong>（認証/ACL/TLS/ICAP）として説明できる

---

<a id="p6"></a>
## 6. 自動化と再現性（Verification）

<p style="margin:.2em 0 1em 0;">
本章は「長いログの貼り付け」ではなく、<strong>完走したことが一目で分かる証跡</strong>に絞っています。  
詳細な説明やスクリプト一覧は <strong><a href="./automation.html">automation</a></strong> を参照してください。
</p>

<figure style="margin: 1.2em auto; text-align:center;">
  <a href="./images/all_in_one_overview.png" target="_blank" rel="noopener">
    <img
      src="./images/all_in_one_overview.png"
      alt="ALL-IN-ONE rebuild overview"
      loading="lazy"
      style="
        display:block;
        margin:0 auto;
        width:100%;
        max-width:1200px;
        height:auto;
        cursor:zoom-in;
        border:1px solid rgba(0,0,0,.12);
        border-radius:10px;
        box-shadow:0 6px 18px rgba(0,0,0,.10);
      "
    >
  </a>
  <figcaption style="margin-top:.6em; font-size:.92em; opacity:.85;">
    STEP0〜17 を一括実行し、初期化・構築・検証・監視まで到達したことを示す概要ログ。
  </figcaption>
</figure>

**確認できること**
- **環境クリア → 起動 → 初期化 → 認証 → ログ/監視 → ヘルス確認**までを **一括で到達**できる
- 手動介入なしで **同一状態（稼働＋検証完了）へ到達**できる
- 最終的に **総所要時間**を出力し、再現できたことを客観的に示せる

<details>
  <summary><strong>実行証跡（PNG）とコマンド（初学者向け：クリックで開く）</strong></summary>

  <hr>

  <h4>PNG（証跡）</h4>
  <ul>
    <li><code>images/all_in_one_overview.png</code>（概要）</li>
    <li><code>images/all_in_one_summary.png</code>（所要時間のサマリ）</li>
    <li><code>images/healthcheck-output.png</code>（全体ヘルスチェック）</li>
  </ul>

  <h4>コマンド（これだけ）</h4>

  <pre><code>cd /home/login00/multiproxy

# 1) 全再構築（STEP0〜17：クリア→起動→初期化→監視/ログ→ヘルス確認）
./all_in_one_rebuild_and_health.sh

# 2) ヘルスチェックだけ（運用時の確認）
./scripts/multiproxy_health_all.sh</code></pre>

</details>

---

## 補足（読む人向け）

- 本ページは **「動いていることを一目で納得できる」** ことを優先しています  
- 再現性・運用手順は **automation** に集約し、本ページは証跡（図/ログ）に集中しています
