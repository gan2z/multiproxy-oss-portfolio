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
**実際に動作していることを、スクリーンショットとログで裏付ける**ための検証記録です。

設計 → 実装 → 動作 → 切り分け、までを  
**一貫した証跡として確認できる構成**にしています。

> ※ 試験ID（P1-1 など）は詳細版（Runbook）で意味を持つため、  
> この要点版では **表示を省略**し「何を証明するか」を優先しています。

---

## 本システムの特徴（要点）

1. **システム全体が稼働しており、構成図と 1:1 で対応付けて確認できる**
2. **復号点（SSLBump）と中継TLS（stunnel）を分離し、責務境界を明確化**
3. **起動・確認・復旧を自動化し、環境差なく再現可能**
4. **ログ基盤（Loki）により、通信事象を原因まで追跡・説明できる**

---

## 1. index.md の主張 ↔ 検証証跡 対応表（要点）

<div class="table-compact" id="map-table">
  <div class="table-wrap">
    <table class="map-table">
      <thead>
        <tr>
          <th>章</th>
          <th>検証内容</th>
          <th>主要証跡</th>
        </tr>
      </thead>
      <tbody>

        <tr>
          <td>概要</td>
          <td>
            システム全体が稼働しており、構成図と稼働状態を
            <strong>1枚で対応付けて確認できる</strong>
          </td>
          <td>
            <a href="./images/P1-proof-all.png" target="_blank"><code>P1-proof-all.png</code></a>
          </td>
        </tr>

        <tr>
          <td>アーキテクチャ</td>
          <td>
            設計した構成図と、実際の稼働コンテナが
            <strong>1:1 対応</strong>
          </td>
          <td>
            <a href="./images/P2-arch-compare.png" target="_blank"><code>P2-arch-compare.png</code></a>
          </td>
        </tr>

        <tr>
          <td>経路制御（PAC）</td>
          <td>
            クライアント指定の PAC により経路が切り替わり、
            <strong>Proxy ログ差で判別可能</strong>
          </td>
          <td>
            <a href="./images/pac-flow-normal-vs-direct.png" target="_blank">
              <code>pac-flow-normal-vs-direct.png</code>
            </a>
          </td>
        </tr>

        <tr>
          <td>復号 / 暗号化</td>
          <td>
            <strong>復号（SSLBump）と中継TLS（stunnel）を分離</strong>し、
            通信境界と責務を明確化
          </td>
          <td>
            <a href="./images/P4-proof-compare.png" target="_blank">
              <code>P4-proof-compare.png</code>
            </a>
          </td>
        </tr>

        <tr>
          <td>可観測性</td>
          <td>
            通信異常を起点に、
            <strong>Loki で経路・復号点・原因まで追跡可能</strong>
          </td>
          <td>
            <a href="./images/p6-observability-triage.png" target="_blank">
              <code>p6-observability-triage.png</code>
            </a>
          </td>
        </tr>

        <tr>
          <td>自動化</td>
          <td>
            起動・確認・復旧をスクリプトで標準化し、
            <strong>再現性を確保</strong>
          </td>
          <td>
            <a href="./images/healthcheck-output.png" target="_blank">
              <code>healthcheck-output.png</code>
            </a>
          </td>
        </tr>

        <tr>
          <td>切り分け</td>
          <td>
            設計判断を
            <strong>ログ → 仕様 → 設計変更</strong>の流れで説明できる
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

## 2. 全体稼働と構成一致の確認

<div style="text-align:center; margin: 1.2em 0;">
  <a href="./images/P1-proof-all.png" target="_blank">
    <img
      src="./images/P1-proof-all.png"
      alt="全体稼働・構成一致"
      style="
        width:100%;
        max-width:1200px;
        max-height:70vh;
        object-fit:contain;
        border-radius:12px;
        box-shadow:0 6px 18px rgba(0,0,0,.15);
      "
    >
  </a>
</div>

---

## 3. 経路制御と復号点の設計検証

### 経路制御（PAC）

- 通信先に応じて Proxy 経路を切り替え
- 各 Proxy のログ有無により、通過経路を客観的に判別可能

<div style="text-align:center; margin: 1.2em 0;">
  <a href="./images/pac-flow-normal-vs-direct.png" target="_blank">
    <img
      src="./images/pac-flow-normal-vs-direct.png"
      alt="PAC 経路差分"
      style="width:100%; max-width:1200px;"
    >
  </a>
</div>

---

### 復号（SSLBump）と中継TLS（stunnel）

- SSLBump は 1 通信 1 回という制約があるため、復号点を経路で分離
- Proxy 間通信は stunnel により TLS 化し、責務を明確化

<div style="text-align:center; margin: 1.2em 0;">
  <a href="./images/P4-proof-compare.png" target="_blank">
    <img
      src="./images/P4-proof-compare.png"
      alt="SSLBump / stunnel 分離"
      style="width:100%; max-width:1200px;"
    >
  </a>
</div>

---

## 4. ログ基盤（Loki）を用いた切り分け

通信トラブル発生時に、  
**どの経路を通り、どこで復号され、どこで失敗したか**を  
Loki（Grafana Explore）で一貫して追跡できます。

### 切り分けの流れ

1. クライアント視点で通信失敗・遅延を確認
2. Loki で時刻・ドメインを軸にログを横断検索
3. Proxy / stunnel のログ有無と内容から経路・復号点を特定
4. 設計前提（PAC / SSLBump / stunnel）と照合し原因を判断

---

## 5. 設計判断の根拠（失敗事例からの改善）

- **stunnel 分離**  
  暗号化責務を分離することで、Proxy ログを L7 挙動に集中

- **二重 SSLBump の不成立**  
  同一通信への二重復号が TLS エラーを引き起こすことをログで確認し、  
  復号点分離設計へ変更

- **認証のレイヤ分離**  
  Kerberos / LDAP / Proxy を役割分担し、  
  問題発生時に層を即座に特定可能

---

## 補足：表示調整について

- 全体俯瞰を優先し、画像は高さ制限＋クリック拡大前提
- 詳細確認は原寸表示で行う設計

---
