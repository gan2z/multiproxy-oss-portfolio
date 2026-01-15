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

> ※ 再現手順・試験ID単位の詳細は  
> <strong><a href="./verification_detail.html">verification_detail</a></strong> にまとめています。

---

## 本資料の特徴（要点）

1. **全コンポーネントが稼働し、構成図と 1:1 で対応付け可能**
2. **復号（SSLBump）と中継TLS（stunnel）を分離し、責務境界を明確化**
3. **経路制御（PAC）により、通信経路をログで判別可能**
4. **ログ基盤（Loki）により、通信事象を原因まで追跡可能**
5. **起動・確認・復旧を自動化し、環境差なく再現可能**

---

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
          <td>構成図と稼働状態が一致している</td>
          <td>
            <a href="./images/P1-proof-all.png" target="_blank">
              <code>P1-proof-all.png</code>
            </a>
          </td>
        </tr>

        <tr>
          <td>アーキテクチャ</td>
          <td>設計構成と実コンテナの 1:1 対応</td>
          <td>
            <a href="./images/P2-arch-compare.png" target="_blank">
              <code>P2-arch-compare.png</code>
            </a>
          </td>
        </tr>

        <tr>
          <td>経路制御</td>
          <td>PAC により経路が切り替わる</td>
          <td>
            <a href="./images/pac-flow-normal-vs-direct.png" target="_blank">
              <code>pac-flow-normal-vs-direct.png</code>
            </a>
          </td>
        </tr>

        <tr>
          <td>復号 / 暗号化</td>
          <td>SSLBump と stunnel の責務分離</td>
          <td>
            <a href="./images/P4-proof-compare.png" target="_blank">
              <code>P4-proof-compare.png</code>
            </a>
          </td>
        </tr>

        <tr>
          <td>可観測性</td>
          <td>通信異常を原因まで追跡可能</td>
          <td>
            <a href="./images/p6-observability-triage.png" target="_blank">
              <code>p6-observability-triage.png</code>
            </a>
          </td>
        </tr>

        <tr>
          <td>自動化</td>
          <td>起動・確認・復旧の再現性</td>
          <td>
            <a href="./images/healthcheck-output.png" target="_blank">
              <code>healthcheck-output.png</code>
            </a>
          </td>
        </tr>

      </tbody>
    </table>
  </div>
</div>

---

## 1. 全体稼働と構成要素一致の確認

<!-- 画像：見やすさ（余白/枠/影）＋タップで原寸（新規タブ） -->
<figure style="margin: 1.2em auto; text-align:center;">
  <a href="./images/P1-proof-all.png" target="_blank" rel="noopener">
    <img
      src="./images/P1-proof-all.png"
      alt="全体稼働（healthcheck / docker ps）確認"
      loading="lazy"
      style="
        width:100%;
        max-width:1400px;
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
    <strong>“構成図の形そのもの”ではなく、稼働対象の「構成要素（コンテナ/サービス）」が欠けていないこと</strong>を確認しています。
  </figcaption>
</figure>

**確認できること**
- 全コンテナ（構成要素）が **running/healthy** で稼働している（部分稼働ではない）
- 監視・ログ・認証・DNS・Proxy/トンネル等の **横断要素も含めて**稼働している
- 「構成図との1:1一致」ではなく、**構成要素（採用コンポーネント）単位で欠けがないこと**を確認している  
  （※構成図は“通信経路/責務”、ここは“稼働チェック（inventory/health）”の証跡）

<details>
  <summary><strong>構成要素（すべて OSS）※クリックで開く</strong></summary>

  <p style="margin:.8em 0 .2em 0; opacity:.9;">
    ※ index.md にも記載していますが、本章では「稼働確認（healthcheck）」の観点として構成要素を併記します。
  </p>

  <div class="table-compact" id="stack-table">
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

## 2. アーキテクチャと構成対応

<!-- 画像：見やすさ（余白/枠/影）＋タップで原寸（新規タブ） -->
<figure style="margin: 1.2em auto; text-align:center;">
  <a href="./images/P2-arch-compare.png" target="_blank" rel="noopener">
    <img
      src="./images/P2-arch-compare.png"
      alt="アーキテクチャ図と稼働コンポーネント（docker ps/health）の対応"
      loading="lazy"
      style="
        width:100%;
        max-width:1400px;
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
    <strong>設計（役割/責務）と、実装（起動している構成要素）が1:1で対応していること</strong>を確認しています。
  </figcaption>
</figure>

**確認できること**
- 設計した役割（入口／分岐／出口）が、実装（Proxy1/2/3・トンネル・検査・認証）に反映されている
- 「再設計したこと」を主張したいのではなく、<strong>実務で扱うような複合システムを、未知の検証環境でも“自分で設計して構築できるか”</strong>を確認している
- アーキテクチャ図（責務・通信経路）と、稼働コンポーネント（health/dps）が <strong>欠けなく起動している</strong>ことを示せる

---

## 3. 経路制御（PAC）の検証

<div style="text-align:center; margin: 1.2em 0;">
  <a href="./images/pac-flow-normal-vs-direct.png" target="_blank">
    <img src="./images/pac-flow-normal-vs-direct.png" style="width:100%; max-width:1200px;">
  </a>
</div>

**確認できること**
- PAC により通常経路 / DIRECT 経路が切り替わる
- Proxy ごとのログ差から、通過経路を客観的に判断できる

---

## 4. 復号（SSLBump）と中継TLS（stunnel）

<div style="text-align:center; margin: 1.2em 0;">
  <a href="./images/P4-proof-compare.png" target="_blank">
    <img src="./images/P4-proof-compare.png" style="width:100%; max-width:1200px;">
  </a>
</div>

**確認できること**
- SSLBump は 1 通信 1 回であるという制約を踏まえた設計
- 復号と中継暗号化を分離し、TLS 境界が明確になっている

---

## 5. ログ基盤（Loki）を用いた切り分け

<div style="text-align:center; margin: 1.2em 0;">
  <a href="./images/p6-observability-triage.png" target="_blank">
    <img src="./images/p6-observability-triage.png" style="width:100%; max-width:1200px;">
  </a>
</div>

**確認できること**
- どの経路を通り、どこで失敗したかを時系列で追跡可能
- 「結果」ではなく「原因レイヤ」で説明できる

---

## 6. 自動化と再現性

<div style="text-align:center; margin: 1.2em 0;">
  <a href="./images/healthcheck-output.png" target="_blank">
    <img src="./images/healthcheck-output.png" style="width:100%; max-width:1200px;">
  </a>
</div>

**確認できること**
- 起動・確認・復旧がスクリプトで標準化されている
- 環境破棄後でも同一状態を再現できる

---

## 補足

- 本ページは **「動いていることを一目で納得できる」** ことを重視
- 詳細な再現手順・試験観点は `verification_detail.html` に委譲

---
