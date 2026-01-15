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
**5分で全体把握 → 10分で設計判断の深掘り**ができるよう、見る順番を固定しています。

### 本ポートフォリオの売り（4本柱）

1. **全体が稼働しており、構成図と 1:1 で確認できる**
2. **復号点（SSLBump）と中継TLS（stunnel）を分離設計している**
3. **自動化により、どんな環境でも再現できる**
4. **ログ基盤により、②の設計判断を“事象→原因”として説明できる**

---

### ✅ まず見るべき3枚（5分コース）
1) **P1：全体が稼働している**（Health / docker ps / 構成一致）  
2) **P3：PAC による経路切替**（NORMAL vs P2-DIRECT のログ差）  
3) **P4：復号点と中継TLSの分離**（SSLBump / stunnel）

> 方針：index.md は「設計思想と構造」、本ページは「動作している根拠」を提示します。

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
          <td>1. 概要</td>
          <td>P1-1, P1-2</td>
          <td>
            システム全体が稼働しており、構成図と稼働状態を
            <strong>1枚で対応付けて確認できる</strong>
          </td>
          <td>
            <a href="./images/P1-proof-all.png" target="_blank"><code>P1-proof-all.png</code></a><br>
            （Health / docker ps / 構成対応 / 主要コンポーネント）
          </td>
        </tr>

        <tr>
          <td>2. アーキテクチャ</td>
          <td>P2-1</td>
          <td>設計した構成図と、実際の稼働コンテナが <strong>1:1 対応</strong></td>
          <td>
            <a href="./images/P2-arch-compare.png" target="_blank"><code>P2-arch-compare.png</code></a>
          </td>
        </tr>

        <tr>
          <td>2. 技術スタック</td>
          <td>P2-2</td>
          <td>イメージタグではなく <strong>実バイナリのバージョン</strong>を把握</td>
          <td>
            <a href="./images/P2-version-check.png" target="_blank"><code>P2-version-check.png</code></a>
          </td>
        </tr>

        <tr>
          <td>3. 経路制御（PAC）</td>
          <td>P3-1, P3-2</td>
          <td>
            クライアントで指定した <strong>PAC により経路が切り替わり</strong>、
            Proxyログ差で証明できる
          </td>
          <td>
            <a href="./images/pac-flow-normal-vs-direct.png" target="_blank">
              <code>pac-flow-normal-vs-direct.png</code>
            </a>
          </td>
        </tr>

        <tr>
          <td>4. 復号 / 暗号化</td>
          <td>P4-1, P4-2</td>
          <td>
            <strong>復号（SSLBump）と中継TLS（stunnel）を分離</strong>し、
            責務境界を明確化
          </td>
          <td>
            <a href="./images/P4-proof-compare.png" target="_blank">
              <code>P4-proof-compare.png</code>
            </a>
          </td>
        </tr>

        <tr>
          <td>6. 可観測性</td>
          <td>P6-1〜P6-4</td>
          <td>
            兆候検知 → 絞り込み → 原因特定を
            <strong>GUI/ログで一気通貫に説明できる</strong>
          </td>
          <td>
            <a href="./images/p6-observability-triage.png" target="_blank">
              <code>p6-observability-triage.png</code>
            </a>
          </td>
        </tr>

        <tr>
          <td>7. 自動化</td>
          <td>P7-1, P7-2</td>
          <td>
            手作業に頼らず <strong>起動・確認・復旧を自動化</strong>し、
            再現性を確保
          </td>
          <td>
            <a href="./images/healthcheck-output.png" target="_blank">
              <code>healthcheck-output.png</code>
            </a>
          </td>
        </tr>

        <tr>
          <td>8. 切り分け力</td>
          <td>P5 / P8</td>
          <td>
            設計判断の背景を、
            <strong>失敗ログ・仕様・挙動</strong>から説明できる
          </td>
          <td>
            <a href="./images/p5-design-trace.png" target="_blank">
              <code>p5-design-trace.png</code>
            </a>
          </td>
        </tr>

      </tbody>
    </table>
  </div>
</div>

---

## 2. 面接官向け：まず見る「比較画像」ショーケース（最短で伝える）

### P1：全体が動いている（Health / 構成一致）
<a id="p1-showcase"></a>

<div style="text-align:center; margin: 1.2em 0;">
  <a href="./images/P1-proof-all.png" target="_blank">
    <img
      src="./images/P1-proof-all.png"
      alt="P1 全体稼働・構成一致"
      style="
        width:100%;
        max-width:1200px;
        max-height:70vh;
        object-fit:contain;
        border-radius:12px;
        box-shadow:0 6px 18px rgba(0,0,0,.15);
        cursor: zoom-in;
      "
    >
  </a>
</div>
<p style="text-align:center; font-size:.9em; opacity:.8;">
※ 高さを制限し、横スクロールなしで全体を把握できるよう調整しています
</p>

---

### P3：PAC による経路切替（NORMAL vs P2-DIRECT）
<a id="p3-showcase"></a>

<div style="text-align:center; margin: 1.2em 0;">
  <a href="./images/pac-flow-normal-vs-direct.png" target="_blank">
    <img
      src="./images/pac-flow-normal-vs-direct.png"
      alt="PAC 経路差分"
      style="width:100%; max-width:1200px; height:auto;
             border-radius:12px;
             box-shadow:0 6px 18px rgba(0,0,0,.15);"
    >
  </a>
</div>

---

### P4：復号点と中継TLSの分離（SSLBump / stunnel）
<a id="p4-showcase"></a>

<div style="text-align:center; margin: 1.2em 0;">
  <a href="./images/P4-proof-compare.png" target="_blank">
    <img
      src="./images/P4-proof-compare.png"
      alt="SSLBump / stunnel 分離"
      style="width:100%; max-width:1200px; height:auto;
             border-radius:12px;
             box-shadow:0 6px 18px rgba(0,0,0,.15);"
    >
  </a>
</div>

---

## 3. 切り分け力（設計判断の根拠をどう説明するか）

### 結論
本システムでは「動いた／動かない」で終わらせず、  
**失敗 → ログ → 仕様 → 設計変更**を一連で説明できる。

### P5-1：なぜ stunnel を分離したか
- Squid に暗号化責務を持たせると、障害切り分けが困難
- TLS を stunnel に分離し、Proxy は L7 処理に専念

### P5-2：二重 SSLBump が破綻したログ
- 同一セッションでの二重復号により TLS が破綻
- 復号点を「経路で分離」する設計に変更

### P5-3：認証をレイヤ別に切り分ける観点
- Kerberos：ユーザ認証
- LDAP：属性参照
- Proxy：通信制御
→ 問題発生時に「どの層か」を即座に特定可能

---

## 補足：画像が見づらい場合の対策について

- **height:auto をやめ、max-height:70vh + object-fit:contain**
- 1枚目（P1）は「全体俯瞰」、以降は「差分に集中」
- 細部はクリック拡大前提に割り切る

→ 面接では「まず全体 → 気になる所だけ拡大」が最も説明しやすくなります。

---
