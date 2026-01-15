---
title: Verification
layout: default
---

# Verification
（OSS Multi-Layer Proxy & Authentication System）

Version: 2025-01-16
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

- <a href="#highlights">要点</a>
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

<p style="margin:.6em 0 0 0; opacity:.9;">
読み方のおすすめは「①対応表 → ②PAC（経路の裏取り） → ③SSLBump/stunnel（設計判断） → ④Loki（切り分け能力） → ⑤自動化（再現性）」です。
面接では “なぜその設計にしたか” を説明するために、失敗例（4-1）と切り分け例（5-3）が特に効きます。
</p>

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
          <td>構成要素が欠けなく稼働している（部分稼働ではない）</td>
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
            <a href="./images/pac-flow-normal-vs-direct.png" target="_blank" rel="noopener"><code>pac-flow-normal-vs-direct.png</code></a><br>
            <a href="./images/pac-client-setting.png" target="_blank" rel="noopener"><code>pac-client-setting.png</code></a><br>
            <a href="./images/pac-proof-proxy1.png" target="_blank" rel="noopener"><code>pac-proof-proxy1.png</code></a><br>
            <a href="./images/pac-proof-proxy2.png" target="_blank" rel="noopener"><code>pac-proof-proxy2.png</code></a><br>
            <a href="./images/pac-proof-direct.png" target="_blank" rel="noopener"><code>pac-proof-direct.png</code></a>
          </td>
        </tr>

        <tr>
          <td>復号 / 暗号化</td>
          <td>SSLBump と stunnel の責務分離（復号点とTLS境界が明確）</td>
          <td>
            <a href="./images/double-bump-error.png" target="_blank" rel="noopener"><code>double-bump-error.png</code></a><br>
            <a href="./images/double-sslbump-proxy1-access-google-500.png" target="_blank" rel="noopener"><code>double-sslbump-proxy1-access-google-500.png</code></a><br>
            <a href="./images/double-sslbump-proxy2-access3129.png" target="_blank" rel="noopener"><code>double-sslbump-proxy2-access3129.png</code></a><br>
            <a href="./images/P4-proof-compare.png" target="_blank" rel="noopener"><code>P4-proof-compare.png</code></a>
          </td>
        </tr>

        <tr>
          <td>可観測性（Loki）</td>
          <td>経路と失敗点を時系列で追い、原因レイヤで説明できる</td>
          <td>
            <a href="./images/p6-observability-triage.png" target="_blank" rel="noopener"><code>p6-observability-triage.png</code></a><br>
            <a href="./images/p6-loki-caseA-proxy1-407.png" target="_blank" rel="noopener"><code>p6-loki-caseA-proxy1-407.png</code></a><br>
            <a href="./images/p6-loki-caseA-proxy2-nohit.png" target="_blank" rel="noopener"><code>p6-loki-caseA-proxy2-nohit.png</code></a><br>
            <a href="./images/p6-loki-caseB-proxy2-403.png" target="_blank" rel="noopener"><code>p6-loki-caseB-proxy2-403.png</code></a><br>
            <a href="./images/p6-loki-caseB-proxy1-nohit.png" target="_blank" rel="noopener"><code>p6-loki-caseB-proxy1-nohit.png</code></a><br>
            <a href="./images/p6-loki-caseB-proxy2-cache-denied.png" target="_blank" rel="noopener"><code>p6-loki-caseB-proxy2-cache-denied.png</code></a>
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

<!-- 画像：見やすさ（余白/枠/影）＋タップで原寸（新規タブ） -->
<figure style="margin: 1.2em auto; text-align:center;">
  <a href="./images/P1-proof-all.png" target="_blank" rel="noopener">
    <img src="./images/P1-proof-all.png" alt="全体稼働（healthcheck / docker ps）確認" loading="lazy" style=" width:100%; max-width:1400px; height:auto; cursor:zoom-in; border:1px solid rgba(0,0,0,.12); border-radius:10px; box-shadow:0 6px 18px rgba(0,0,0,.10); " >
  </a>
  <figcaption style="margin-top:.6em; font-size:.92em; opacity:.85;">
    クリック/タップで原寸表示（別タブ）。
    <strong>構成要素（コンテナ/サービス）が欠けていない</strong>ことを確認しています。
  </figcaption>
</figure>

**確認できること**
- 全コンテナ（構成要素）が **running/healthy** で稼働している（部分稼働ではない）
- Proxy だけでなく、**認証（AD/LDAP/Kerberos）・DNS・ICAP・ログ・監視**などの横断要素も含めて稼働している
- ここでの証明は「構成図の線が正しい」ではなく、**“運用できる最小条件（inventory/health）” が整っている**という意味での稼働証跡

<details>
  <summary><strong>構成要素（すべて OSS）※クリックで開く</strong></summary>

  <p style="margin:.8em 0 .2em 0; opacity:.9;">
    ※ index.md にも記載していますが、本章では「稼働確認（healthcheck）」の観点として構成要素を併記します。
  </p>

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
            <td>dnsmasq（Split DNS）</td>
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

<!-- 画像：見やすさ（余白/枠/影）＋タップで原寸（新規タブ） -->
<figure style="margin: 1.2em auto; text-align:center;">
  <a href="./images/P2-arch-compare.png" target="_blank" rel="noopener">
    <img src="./images/P2-arch-compare.png" alt="アーキテクチャ図と稼働コンポーネント（docker ps/health）の対応" loading="lazy" style=" width:100%; max-width:1400px; height:auto; cursor:zoom-in; border:1px solid rgba(0,0,0,.12); border-radius:10px; box-shadow:0 6px 18px rgba(0,0,0,.10); " >
  </a>
  <figcaption style="margin-top:.6em; font-size:.92em; opacity:.85;">
    クリック/タップで原寸表示（別タブ）。
    <strong>設計（役割/責務）と、実装（起動している構成要素）が1:1で対応していること</strong>を確認しています。
  </figcaption>
</figure>

**確認できること**
- 入口（Proxy1）/ 分岐（Proxy2）/ 出口（Proxy3）の役割分担が、実装と一致している
- Proxy 間の暗号化（stunnel）、検査（ICAP/ClamAV）、認証（AD/LDAP/Kerberos）、可観測性（Loki/Graylog/OpenSearch）、監視（Zabbix）が **“経路の外側（横断）”** として組み込まれている
- “プロキシ3台を繋いだ” ではなく、**実務で扱うような複合要素（認証・検査・ログ・監視）を含む構成**として成立している

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

<!-- 画像：見やすさ（余白/枠/影）＋タップで原寸（新規タブ） -->
<figure style="margin: 1.2em auto; text-align:center;">
  <a href="./images/pac-flow-normal-vs-direct.png" target="_blank" rel="noopener">
    <img src="./images/pac-flow-normal-vs-direct.png" alt="PACによる経路制御の全体概要" loading="lazy" style=" width:100%; max-width:1400px; height:auto; cursor:zoom-in; border:1px solid rgba(0,0,0,.12); border-radius:10px; box-shadow:0 6px 18px rgba(0,0,0,.10); " >
  </a>
  <figcaption style="margin-top:.6em; font-size:.92em; opacity:.85;">
    クリック/タップで原寸表示（別タブ）。
    PAC により <strong>Proxy1 / Proxy2（DIRECT相当） / DIRECT</strong> のいずれを出口として選択するかを整理した全体概要図です。
  </figcaption>
</figure>

**ここで整理しているポイント**
- クライアントは PAC により「通信の出口（経路）」を選択する
- 通常通信は <strong>Proxy1 → Proxy2 → Proxy3</strong> の多段経路
- 条件により以下へ分岐する  
  - <strong>Proxy2 直行</strong>（Proxy1 skip / DIRECT相当）  
  - <strong>DIRECT</strong>（ルーター直通）
- 以降の検証では、<strong>この分岐が “ログの有無” で裏取りできる</strong>ことを示す

<hr>

### 3-1. クライアント側：PAC ファイルの有効化

<!-- 画像：見やすさ（余白/枠/影）＋タップで原寸（新規タブ） -->
<figure style="margin: 1.2em auto; text-align:center;">
  <a href="./images/pac-client-setting.png" target="_blank" rel="noopener">
    <img src="./images/pac-client-setting.png" alt="クライアントでのPAC設定" loading="lazy" style=" width:100%; max-width:1400px; height:auto; cursor:zoom-in; border:1px solid rgba(0,0,0,.12); border-radius:10px; box-shadow:0 6px 18px rgba(0,0,0,.10); " >
  </a>
  <figcaption style="margin-top:.6em; font-size:.92em; opacity:.85;">
    クリック/タップで原寸表示（別タブ）。
    クライアントが PAC（wpad.dat）を取得し、<strong>自動構成スクリプトが有効化</strong>されている状態です。
  </figcaption>
</figure>

**確認できること**
- クライアントが PAC ファイルを正しく取得・適用している
- 以降の通信が PAC 判定を経由する前提が成立している

<hr>

### 3-2. Proxy1 経由の通信（通常経路）

<!-- 画像：見やすさ（余白/枠/影）＋タップで原寸（新規タブ） -->
<figure style="margin: 1.2em auto; text-align:center;">
  <a href="./images/pac-proof-proxy1.png" target="_blank" rel="noopener">
    <img src="./images/pac-proof-proxy1.png" alt="Proxy1経由通信の確認" loading="lazy" style=" width:100%; max-width:1400px; height:auto; cursor:zoom-in; border:1px solid rgba(0,0,0,.12); border-radius:10px; box-shadow:0 6px 18px rgba(0,0,0,.10); " >
  </a>
  <figcaption style="margin-top:.6em; font-size:.92em; opacity:.85;">
    クリック/タップで原寸表示（別タブ）。
    Proxy1 → Proxy2 → Proxy3 の通常経路で通信していることを、<strong>Proxy1 のアクセスログ</strong>から確認しています。
  </figcaption>
</figure>

**確認できること**
- PAC 判定により Proxy1 が選択されている
- Proxy1 のアクセスログに通信が記録されている
- 通常経路が正しく機能している

<hr>

### 3-3. Proxy2 経由の通信（Proxy1 skip / DIRECT 相当）

<!-- 画像：見やすさ（余白/枠/影）＋タップで原寸（新規タブ） -->
<figure style="margin: 1.2em auto; text-align:center;">
  <a href="./images/pac-proof-proxy2.png" target="_blank" rel="noopener">
    <img src="./images/pac-proof-proxy2.png" alt="Proxy2経由通信の確認" loading="lazy" style=" width:100%; max-width:1400px; height:auto; cursor:zoom-in; border:1px solid rgba(0,0,0,.12); border-radius:10px; box-shadow:0 6px 18px rgba(0,0,0,.10); " >
  </a>
  <figcaption style="margin-top:.6em; font-size:.92em; opacity:.85;">
    クリック/タップで原寸表示（別タブ）。
    Proxy1 を経由せず Proxy2 から通信していることを、<strong>Proxy1 / Proxy2 のログ差分</strong>で確認しています。
  </figcaption>
</figure>

**確認できること**
- Proxy1 にはログが残らない（＝通っていない）
- Proxy2 のみに通信ログが記録されている（＝直行が成立）
- “経路②（Proxy1 skip）” が実通信として成立している

<hr>

### 3-4. DIRECT（ルーター直通）通信

<!-- 画像：見やすさ（余白/枠/影）＋タップで原寸（新規タブ） -->
<figure style="margin: 1.2em auto; text-align:center;">
  <a href="./images/pac-proof-direct.png" target="_blank" rel="noopener">
    <img src="./images/pac-proof-direct.png" alt="DIRECT通信の確認" loading="lazy" style=" width:100%; max-width:1400px; height:auto; cursor:zoom-in; border:1px solid rgba(0,0,0,.12); border-radius:10px; box-shadow:0 6px 18px rgba(0,0,0,.10); " >
  </a>
  <figcaption style="margin-top:.6em; font-size:.92em; opacity:.85;">
    クリック/タップで原寸表示（別タブ）。
    Proxy1 / Proxy2 のいずれにもログが残らず、<strong>ルーターへ直接通信している</strong>ことを確認しています。
  </figcaption>
</figure>

**確認できること**
- PAC 判定により DIRECT が選択されている
- （本システムの）Proxy1/Proxy2/Proxy3 を経由しない DIRECT 通信が成立している

<hr>

### 3-5. 検証サマリ（PAC 経路制御）

**本章で証明できたこと**
- PAC により <strong>通信経路を動的に切り替えられる</strong>
- 設計した分岐（Proxy1 / Proxy2 / DIRECT）が <strong>実通信として成立</strong>
- 判定結果を <strong>Proxy 側ログの有無</strong>で客観的に確認できる
- 「設定したつもり」ではなく、<strong>挙動として説明できる状態</strong>になっている

---

<a id="p4"></a>
## 4. 復号（SSLBump）と中継 TLS（stunnel）

<p style="margin:.4em 0 1em 0;">
本章では、HTTPS 復号（SSLBump）に関する<strong>制約と失敗例</strong>を明示した上で、  
その制約を踏まえて <strong>復号（SSLBump）と中継暗号化（stunnel）を分離設計</strong>したことを検証します。
</p>

<hr>

### 4-0. 前提整理：SSLBump は「1 通信につき 1 回のみ」

    [ Client ]
        |
        |  HTTPS (TLS)
        v
    +-------------------+
    | Proxy1            |
    |-------------------|
    | SSLBump (復号)    |  ← ★ HTTPS復号はここで1回のみ
    +-------------------+
        |
        |  HTTP (復号済み)
        |  + TLS (stunnel)
        v
    +-------------------+
    | Proxy2            |
    |-------------------|
    | 制御 / 中継のみ   |  ← 復号はしない
    +-------------------+
        |
        |  HTTP (復号済み)
        |  + TLS (stunnel)
        v
    +-------------------+
    | Proxy3            |
    |-------------------|
    | 最終出口          |
    +-------------------+
        |
        v
    [ Internet ]

**設計上の重要な前提**
- SSLBump は同一 TLS セッションに対して <strong>二重適用できない</strong>
- 多段 Proxy 構成では「どこで復号するか」を明確にしないと通信が破綻する
- Proxy 間の暗号化は SSLBump ではなく <strong>stunnel（純粋な TLS）</strong>で担保する必要がある

<hr>

### 4-1. 失敗例：Proxy1 / Proxy2 の二重 SSLBump によるエラー

<!-- 画像：見やすさ（余白/枠/影）＋タップで原寸（新規タブ） -->
<figure style="margin: 1.2em auto; text-align:center;">
  <a href="./images/double-bump-error.png" target="_blank" rel="noopener">
    <img src="./images/double-bump-error.png" alt="二重SSLBumpによるブラウザエラー" loading="lazy" style=" width:100%; max-width:1400px; height:auto; cursor:zoom-in; border:1px solid rgba(0,0,0,.12); border-radius:10px; box-shadow:0 6px 18px rgba(0,0,0,.10); " >
  </a>
  <figcaption style="margin-top:.6em; font-size:.92em; opacity:.85;">
    クリック/タップで原寸表示（別タブ）。二重SSLBumpによりブラウザ側でエラーとなる例です。
  </figcaption>
</figure>

<!-- 画像：見やすさ（余白/枠/影）＋タップで原寸（新規タブ） -->
<figure style="margin: 1.2em auto; text-align:center;">
  <a href="./images/double-sslbump-proxy1-access-google-500.png" target="_blank" rel="noopener">
    <img src="./images/double-sslbump-proxy1-access-google-500.png" alt="Proxy1側のエラーログ（入口）" loading="lazy" style=" width:100%; max-width:1400px; height:auto; cursor:zoom-in; border:1px solid rgba(0,0,0,.12); border-radius:10px; box-shadow:0 6px 18px rgba(0,0,0,.10); " >
  </a>
  <figcaption style="margin-top:.6em; font-size:.92em; opacity:.85;">
    クリック/タップで原寸表示（別タブ）。Proxy1（入口）側にエラーが記録され、失敗をログで追跡できることを示します。
  </figcaption>
</figure>

<!-- 画像：見やすさ（余白/枠/影）＋タップで原寸（新規タブ） -->
<figure style="margin: 1.2em auto; text-align:center;">
  <a href="./images/double-sslbump-proxy2-access3129.png" target="_blank" rel="noopener">
    <img src="./images/double-sslbump-proxy2-access3129.png" alt="Proxy2側のSSL再復号ログ（3129）" loading="lazy" style=" width:100%; max-width:1400px; height:auto; cursor:zoom-in; border:1px solid rgba(0,0,0,.12); border-radius:10px; box-shadow:0 6px 18px rgba(0,0,0,.10); " >
  </a>
  <figcaption style="margin-top:.6em; font-size:.92em; opacity:.85;">
    クリック/タップで原寸表示（別タブ）。Proxy2 側でも復号を試みることで、同一セッションへの二重復号となり破綻する例です。
  </figcaption>
</figure>

**ここで確認できること**
- Proxy1 / Proxy2 の両方で SSLBump を行うと通信が成立しない
- ブラウザエラーと Proxy 側ログから <strong>二重復号が原因</strong>であることを確認できる
- これは「設定ミス」ではなく <strong>SSLBump の仕様上の制約</strong>である

<hr>

### 4-2. 改善後の設計：復号と中継暗号化の分離

**最終設計方針**
- SSLBump（復号）は <strong>Proxy1 のみ</strong>で実施
- Proxy 間通信は <strong>stunnel による TLS</strong>で暗号化
- Proxy2 / Proxy3 では SSLBump を行わず、<strong>復号済み通信を制御・中継</strong>

**この設計で得られる効果**
- SSLBump の制約（1通信1回）を厳密に遵守
- 復号ポイントと TLS 境界が明確
- 多段 Proxy 構成でも HTTPS 通信が安定して成立
- 失敗時に「復号が原因か」「中継TLSが原因か」をレイヤ分離して切り分けやすい

<hr>

### 4-3. 検証サマリ（SSLBump / stunnel）

**本章で証明できたこと**
- SSLBump は <strong>1 通信 1 回</strong>という制約を持つ
- 二重 SSLBump は実際に通信障害を引き起こす
- 復号（SSLBump）と中継暗号化（stunnel）を分離することで問題を解消できる
- 設計判断が <strong>実ログと失敗事例に基づいている</strong>ことを示せている

---

<a id="p5"></a>
## 5. ログ基盤（Loki）を用いた切り分け

<p style="margin:.4em 0 1em 0;">
本章では、Loki（Promtail 収集）により <strong>Proxy1 / Proxy2 / Proxy3 の Squid ログ</strong>を横断検索し、
「どの経路を通り、どのレイヤで失敗したか」を<strong>時系列で切り分け</strong>できることを示します。<br>
以降の検証では、<strong>アクセスログ（access.log）</strong>と<strong>キャッシュログ（cache.log）</strong>を中心に追跡します。
</p>

<hr>

<!-- 画像：見やすさ（余白/枠/影）＋タップで原寸（新規タブ） -->
<figure style="margin: 1.2em auto; text-align:center;">
  <a href="./images/p6-observability-triage.png" target="_blank" rel="noopener">
    <img src="./images/p6-observability-triage.png" alt="Lokiによる経路と失敗点の切り分け" loading="lazy" style=" width:100%; max-width:1400px; height:auto; cursor:zoom-in; border:1px solid rgba(0,0,0,.12); border-radius:10px; box-shadow:0 6px 18px rgba(0,0,0,.10); " >
  </a>
  <figcaption style="margin-top:.6em; font-size:.92em; opacity:.85;">
    クリック/タップで原寸表示（別タブ）。
    Proxy1/2/3 のログを Loki で横断し、経路と失敗点を<strong>時系列で追跡</strong>するイメージです。
  </figcaption>
</figure>

**確認できること**
- どの経路を通り、どこで失敗したかを <strong>時系列で追跡可能</strong>
- 「結果」ではなく「原因レイヤ（認証 / TLS / 経路 / ICAP / ACL）」で <strong>説明できる</strong>

<hr>

### 5-1. 収集対象（Promtail → Loki）

<p style="margin:.2em 0 1em 0;">
Promtail は 3段 Squid のログ（squid1/2/3）を<strong>別ラベル</strong>で収集し、Loki に格納します。<br>
（例：access.log / cache.log をコンテナ別に追跡できる）
</p>

<ul>
  <li><strong>Squid（Proxy1 / Proxy2 / Proxy3）</strong>：access.log / cache.log</li>
  <li><strong>ICAP</strong>：c-icap / clamav のログ（必要に応じて）</li>
  <li><strong>stunnel</strong>：TLS 中継のログ（必要に応じて）</li>
</ul>

<hr>

### 5-2. 切り分け手順（Lokiで見る順番）

**見る順番（おすすめ）**
1) <strong>Proxy1 access.log</strong>：クライアントの入口として最初に観測される（PAC/認証/SSLBumpの起点）  
2) <strong>Proxy2 access.log</strong>：経路分岐・ICAP・上流中継の観測点（3129/3131 を分離して確認）  
3) <strong>Proxy3 access.log</strong>：最終出口（上流接続の成否）  
4) <strong>cache.log</strong>：TLS/SSLBump/ヘルパー/ICAP/ACL の詳細（失敗原因の裏取り）

<hr>

### 5-3. 実例：Loki で「入口(407)」と「出口側(403)」を切り分けた

本章では、同じクライアントからの HTTPS 通信に対して、以下 2 つの「失敗」を意図的に発生させ、  
Loki 上で <strong>どこで止まったか</strong>／<strong>なぜ止まったか</strong>を説明できることを示します。

---

#### Case A：入口（Proxy1）で 407（認証要求） → Proxy2 まで到達していない

**打鍵（時刻の基準）**
- `date` : 2026-01-16 03:20:29 JST

**打鍵（Proxy1 経由で google に CONNECT）**
- `curl --proxy http://proxy1.ad.lan:3128 https://www.google.com/`  
  → 結果：<strong>HTTP/1.1 407 Proxy Authentication Required</strong>

**観測（access.log の差分）**
- Proxy1 access.log に <strong>status=407</strong> が記録される  
  - `url="www.google.com:443" status=407 hier=TCP_DENIED:HIER_NONE`
- Proxy2 access_3129.log は <strong>空（NO HIT）</strong>  
  → <strong>入口(Proxy1)で止まり、鎖（Proxy1→Proxy2）へ到達していない</strong>ことが確定

**Loki での見方（例：LogQL）**
- Proxy1 の 407 を拾う
  - `{job="squid", instance="proxy1"} |= "www.google.com" |= "status=407"`
- Proxy2 側に同一ホストが出ていないことを確認（ヒット無しが期待値）
  - `{job="squid", instance="proxy2"} |= "www.google.com"`

**スクリーンショット（証跡）**
- `images/p6-loki-caseA-proxy1-407.png`
- `images/p6-loki-caseA-proxy2-nohit.png`

---

#### Case B：出口側（Proxy2:3131）で 403（ACL 拒否） → Proxy1 を経由していない（直行）

**前提**  
Proxy2:3131 は「クライアント直行の出口（経路②）」として利用し、認証は Kerberos（Negotiate）で通す。

**打鍵（まず失敗：認証なし）**
- `curl --proxy http://proxy2.ad.lan:3131 https://www.example.com/`  
  → 結果：<strong>HTTP/1.1 407</strong>（Negotiate 必須のため）

**打鍵（成功：Kerberos 認証あり）**
- `kinit Administrator@AD.LAN` → `klist`（HTTP/proxy2.ad.lan のチケット取得）
- `curl --proxy http://proxy2.ad.lan:3131 --proxy-negotiate -U : https://www.example.com/`  
  → 結果：<strong>CONNECT 200 → GET 200</strong>（経路②が成立）

**デモ（意図的に 403 を作る：Proxy2:3131 だけ Wikipedia を拒否）**
- Proxy2 に一時ルール投入（例：`98-demo-deny-wikipedia-3131.conf`）
  - `acl demo_wiki dstdomain .wikipedia.org`
  - `http_access deny p2_client_3131 auth_users demo_wiki`
- その後、
  - `curl --proxy http://proxy2.ad.lan:3131 --proxy-negotiate -U : https://www.wikipedia.org/`  
    → 結果：<strong>HTTP/1.1 403 Forbidden</strong>

**観測（access.log の差分）**
- Proxy1 access.log：`wikipedia` が <strong>出ない（NO HIT）</strong>  
  → <strong>入口(Proxy1)を通っていない</strong>（=直行の証拠）
- Proxy2 access_3131.log：以下が連続で記録される  
  - `CONNECT www.wikipedia.org:443 status=200 user=Administrator@AD.LAN`
  - `GET https://www.wikipedia.org/ status=403`
  → <strong>Proxy2:3131 で「認証は通ったが ACL で拒否された」</strong>と説明できる

**Loki での見方（例：LogQL）**
- Proxy2 で 403 を拾う（結果）
  - `{job="squid", instance="proxy2"} |= "wikipedia" |= "status=403"`
- Proxy1 に出ていないことを確認（経路②の裏取り）
  - `{job="squid", instance="proxy1"} |= "wikipedia"`
- cache.log 側で “DENIED” を拾う（原因レイヤ）
  - `{job="squid", instance="proxy2"} |= "DENIED" |= "wikipedia"`

**スクリーンショット（証跡）**
- `images/p6-loki-caseB-proxy2-403.png`
- `images/p6-loki-caseB-proxy1-nohit.png`
- `images/p6-loki-caseB-proxy2-cache-denied.png`

**後片付け**
- 一時 deny ルールを削除し、`squid -k reconfigure` で復旧（恒久設定に影響しない形で検証）

<hr>

### 5-4. Loki での検索コツ（初学者向け：ここだけ読めば迷わない）

**(1) Explore → Loki を選ぶ**  
左メニュー Explore で Loki を選択する。

**(2) まず Label browser で “使えるラベル” を確定する**  
- 例：`job="squid"` / `instance="proxy1|proxy2|proxy3"`  
※ `{}` のまま検索するとエラーになりやすいので、必ず絞り込みラベルを付ける。

**(3) 時刻範囲を “狭く” する（±2分）**  
`date` の実行時刻を基準に、右上の時間範囲を「前後2分」程度に絞ると追跡が速い。

**(4) “結果→原因” の順に見る**  
- まず access.log で `status=407/403/200` を拾う（結果）  
- 次に cache.log で `DENIED / ERROR / SslBump / helper` を拾う（原因）

<hr>

### 5-5. 検証サマリ（Loki切り分け）

**本章で証明できたこと**
- 3段プロキシ（Proxy1/2/3）のログを Loki で横断検索できる
- 通信が「どの経路を通ったか（Proxy1経由 / Proxy2直行）」をログで裏取りできる
- 失敗を「HTTPコード（結果）」だけでなく、<strong>認証 / ACL / TLS / ICAP</strong>などの <strong>原因レイヤ</strong>として説明できる

---

<a id="p6"></a>
## 6. 自動化と再現性（Verification）

<p style="margin:.2em 0 1em 0;">
本章では「自動化がある」だけでなく、<strong>“完走して同一状態へ到達できた” ことを示す証跡</strong>を提示します。  
スクリプトの一覧・役割・運用の考え方は <strong><a href="./automation.html">automation</a></strong> に整理しています。
</p>

<!-- 画像：見やすさ（余白/枠/影）＋タップで原寸（新規タブ） -->
<figure style="margin: 1.2em auto; text-align:center;">
  <a href="./images/all_in_one_overview.png" target="_blank" rel="noopener">
    <img src="./images/all_in_one_overview.png" alt="ALL-IN-ONE（STEP0〜17）概要ログ" loading="lazy" style=" width:100%; max-width:1400px; height:auto; cursor:zoom-in; border:1px solid rgba(0,0,0,.12); border-radius:10px; box-shadow:0 6px 18px rgba(0,0,0,.10); " >
  </a>
  <figcaption style="margin-top:.6em; font-size:.92em; opacity:.85;">
    クリック/タップで原寸表示（別タブ）。<strong>環境クリア→起動→初期化→監視/ログ→ヘルス確認</strong>までを一括実行した概要です。
  </figcaption>
</figure>

<!-- 画像：見やすさ（余白/枠/影）＋タップで原寸（新規タブ） -->
<figure style="margin: 1.2em auto; text-align:center;">
  <a href="./images/all_in_one_summary.png" target="_blank" rel="noopener">
    <img src="./images/all_in_one_summary.png" alt="ALL-IN-ONE 完了サマリ（所要時間）" loading="lazy" style=" width:100%; max-width:1400px; height:auto; cursor:zoom-in; border:1px solid rgba(0,0,0,.12); border-radius:10px; box-shadow:0 6px 18px rgba(0,0,0,.10); " >
  </a>
  <figcaption style="margin-top:.6em; font-size:.92em; opacity:.85;">
    クリック/タップで原寸表示（別タブ）。完了サマリ（所要時間）を示し、<strong>再現性（到達できること）</strong>を客観的に示します。
  </figcaption>
</figure>

<!-- 画像：見やすさ（余白/枠/影）＋タップで原寸（新規タブ） -->
<figure style="margin: 1.2em auto; text-align:center;">
  <a href="./images/healthcheck-output.png" target="_blank" rel="noopener">
    <img src="./images/healthcheck-output.png" alt="全体ヘルスチェック結果" loading="lazy" style=" width:100%; max-width:1400px; height:auto; cursor:zoom-in; border:1px solid rgba(0,0,0,.12); border-radius:10px; box-shadow:0 6px 18px rgba(0,0,0,.10); " >
  </a>
  <figcaption style="margin-top:.6em; font-size:.92em; opacity:.85;">
    クリック/タップで原寸表示（別タブ）。全体ヘルスチェックにより、<strong>通信検証の前提（依存関係）が整っている</strong>ことを示します。
  </figcaption>
</figure>

**確認できること**
- **環境クリア → 起動 → 初期化 → 認証 → ログ/監視 → ヘルス確認**までを **STEP0〜17** で一括実行できる
- 手動介入なしで **同一状態（稼働＋検証完了）へ到達**できる
- 失敗しやすい箇所は **Guard / Retry / Warn 継続**で吸収し、最後に **総所要時間**を必ず出力する
- “一度動いたら終わり” ではなく、**破棄→再構築→再検証ができる**ため、改善ループ（設計→検証→修正）に集中できる

### 6-1. コマンド（初学者向け：これだけ）

    cd /home/login00/multiproxy

    # 1) 全再構築（STEP0〜17：クリア→起動→初期化→監視/ログ→ヘルス確認）
    ./all_in_one_rebuild_and_health.sh
    
    # 2) ヘルスチェックだけ（運用時の確認）
    ./scripts/multiproxy_health_all.sh

---

## 補足（読む人向け）

- 本ページは **「動いていることを一目で納得できる」** ことを重視しています  
- 再現性・運用手順・スクリプトの全体整理は **automation** に集約しています  
- 詳細な試験観点（Runbook）を載せる場合は `verification_detail.html` に委譲する想定です
