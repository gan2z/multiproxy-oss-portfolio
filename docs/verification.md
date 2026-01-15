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
> `verification_detail.html` に切り出しています。

---

## 本システムの特徴（要点）

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
          <th>対応する証跡</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>全体稼働</td>
          <td>構成図と稼働状態が一致している</td>
          <td>P1-proof-all.png</td>
        </tr>
        <tr>
          <td>アーキテクチャ</td>
          <td>設計構成と実コンテナの 1:1 対応</td>
          <td>P2-arch-compare.png</td>
        </tr>
        <tr>
          <td>経路制御</td>
          <td>PAC により経路が切り替わる</td>
          <td>pac-flow-normal-vs-direct.png</td>
        </tr>
        <tr>
          <td>復号 / 暗号化</td>
          <td>SSLBump と stunnel の責務分離</td>
          <td>P4-proof-compare.png</td>
        </tr>
        <tr>
          <td>可観測性</td>
          <td>通信異常を原因まで追跡可能</td>
          <td>p6-observability-triage.png</td>
        </tr>
        <tr>
          <td>自動化</td>
          <td>起動・確認・復旧の再現性</td>
          <td>healthcheck-output.png</td>
        </tr>
      </tbody>
    </table>
  </div>
</div>

---

## 1. 全体稼働と構成一致の確認

<div style="text-align:center; margin: 1.2em 0;">
  <a href="./images/P1-proof-all.png" target="_blank">
    <img src="./images/P1-proof-all.png" style="width:100%; max-width:1200px;">
  </a>
</div>

**確認できること**
- 全コンテナが稼働している
- 稼働状態が構成図と 1:1 で対応している
- 「部分的に動いている」状態ではないことを示せる

---

## 2. アーキテクチャと構成対応

<div style="text-align:center; margin: 1.2em 0;">
  <a href="./images/P2-arch-compare.png" target="_blank">
    <img src="./images/P2-arch-compare.png" style="width:100%; max-width:1200px;">
  </a>
</div>

**確認できること**
- 設計した役割（入口／分岐／出口）が実装に反映されている
- 商用構成をそのままコピーせず、責務単位で再設計している

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
