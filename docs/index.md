---
title: OSS Multi-Layer Proxy & Authentication System
layout: default
---

# OSS Multi-Layer Proxy & Authentication System  
── 構築・課題・設計思想まとめ

---

## 1. プロジェクト概要（Summary）

本プロジェクトは、実務で扱う **多段プロキシ／認証基盤／ログ基盤／監視基盤** を題材に、  
**WSL2 × Docker × VMware × OSS のみ** を用いて  
**商用品質のネットワーク・セキュリティ基盤をゼロから再構築** した個人ポートフォリオです。

単なる構築手順の再現ではなく、  
**通信経路・ログ・失敗事例を通じて「なぜこの設計が必要か」を理解すること**を目的としています。

### **主要コンセプト**
- 商用設定のコピーは行わない（構造から自作）
- 実務トラブルを **再現 → 可視化 → 理解 → 改善**
- SSL/TLS・認証・経路制御の設計思想を体系的に理解
- 「操作できる人」ではなく「設計できるエンジニア」を目指す


---

## 2. システムアーキテクチャ（全体像）

> ※ `./images/architecture.png` を配置するだけで表示されます

![architecture](./images/architecture.png)


### 構成要素（すべて OSS）

- 多段プロキシ：Squid（3段構成）
- プロキシ間暗号化：stunnel
- 認証基盤：OpenLDAP / Samba AD/DC / Kerberos
- DNS / WPAD：dnsmasq
- ログ基盤：Loki / Graylog / OpenSearch
- 監視：Zabbix（TLS-PSK）
- 自動化：Bash（構築・検証・復旧）
- 仮想化：VMware（AD/DC・Windows クライアント）

---

## ✅ 動作検証（スクリーンショット集）

本ポートフォリオは「設計の主張」だけでなく、  
**実際に動作している証拠（スクショ／ログ）**も合わせて提示しています。

- フェーズ1統合テストの観点（P1〜P13）
- 経路別 SSLBump（通常：Proxy1 / DIRECT：Proxy2）の証拠
- stunnel による中継TLSの証拠
- AD / LDAP / Kerberos の疎通・ユーザ可視化
- Graylog / Loki / Zabbix による可観測性

📌 **検証ページはこちら**：  
- [Verification（検証観点 & スクショ一覧）](./verification.html)


---

## 3. 通信経路とポート設計（今回の構成変更点）

### 3-1. 通信フロー概要

本構成における通信フローの全体像を以下に示します。  
実際の通信経路・ポート・プロトコルの関係は、後述の図面を参照してください。

![通信フロー全体図](./images/communication-flow.png)

---

#### クライアント → Proxy1

- プロトコル：HTTP / HTTPS
- 宛先：Proxy1（Squid）
- リッスンポート：3128
- ログ出力：
  - `/var/log/squid/access.log`

---

#### Proxy1 → Proxy2（通常経路・中継 TLS）

- Proxy1（Squid）
  - parent proxy：`127.0.0.1:13128`
- proxy1-stunnel（client）
  - LISTEN：13128（local）
  - SEND：proxy2-stunnel:4431（TLS）
- proxy2-stunnel（server）
  - LISTEN：4431
  - SEND：proxy2:3129（HTTP）

---

#### Proxy2（経路分岐ポイント）

- Squid LISTEN ポート：
  - **3129**：Proxy1 → Proxy2 通常経路
  - **3131**：クライアント DIRECT 経路
- ログ出力：
  - 3129 → `access_3129.log`
  - 3131 → `access_3131.log`

---

#### Proxy2 → Proxy3（通常経路）

- Proxy2 parent proxy：
  - proxy2-3-stunnel:23129
- proxy2-3-stunnel（client）
  - LISTEN：23129
  - SEND：proxy3-stunnel:4433（TLS）
- proxy3-stunnel（server）
  - LISTEN：4433
  - SEND：proxy3:3130（HTTP）
- Proxy3（Squid）
  - LISTEN：3130
  - LOG：`access_main.log`

---

#### Proxy2 → Proxy3（DIRECT 経路）

- Proxy2 parent proxy：
  - proxy2-3-stunnel:23131
- proxy2-3-stunnel（client）
  - LISTEN：23131
  - SEND：proxy3-stunnel:4434（TLS）
- proxy3-stunnel（server）
  - LISTEN：4434
  - SEND：proxy3:3132（HTTP）
- Proxy3（Squid）
  - LISTEN：3132
  - LOG：`access_direct.log`


---

#### Proxy1 → Proxy2（通常経路）



## 4. 技術スタック

| 区分    | 採用技術                                   |
| ----- | -------------------------------------- |
| プロキシ  | Squid（3段、経路別ポート設計）                     |
| 中継暗号化 | stunnel（1→2 / 2→3）                     |
| 認証    | OpenLDAP / Samba AD/DC / Kerberos      |
| DNS   | dnsmasq（WPAD / Split DNS）              |
| ログ    | Promtail / Loki / Graylog / OpenSearch |
| 監視    | Zabbix（TLS-PSK + Sidecar）              |
| 自動化   | Bash                                   |
| OS    | Ubuntu 24.04（WSL2 mirrored mode）       |

---

## 5. 全体設計（商用プロキシの設計思想を OSS で再構築）

本構成は、商用プロキシ製品で前提とされている  
**通信分離・復号制御・暗号化境界・経路切替** の設計思想を、  
OSS 環境で **自ら分解・再設計したもの** です。

---

### 5-1. SSLBump の復号範囲を経路ごとに設計

商用プロキシ製品では、  
**内部通信を平文のまま扱いつつ、外部からは TLS に見せる機能を ON/OFF で制御**できます。

一方、Squid には  
**復号後の通信を再び TLS 化する機能が存在しない**ため、  
本構成では stunnel を追加し、  
**商用プロキシで内包されている機能を OSS で分離実装**しています。

その上で、  
「どの経路の、どのプロキシで復号を行うか」を  
**通信経路ごとに明示的に設計**しました。

---

#### 経路①：Proxy1 → Proxy2 → Proxy3（通常経路）

- **SSLBump は Proxy1 のみで実施**
- Proxy2 / Proxy3 は復号済み通信の中継・制御を担当
- 復号は 1 度だけ行い、以降は再 MITM しない

Client → Proxy1（SSLBump）
→ Proxy2（中継）
→ Proxy3（出口・ログ）

---

#### 経路②：クライアント → Proxy2（DIRECT 経路）

- **SSLBump は Proxy2 で実施**
- Proxy1 は経由しない
- Proxy2 が入口プロキシとして動作

Client → Proxy2（SSLBump）
→ Proxy3（出口・ログ）

---

#### なぜこの設計に行き着いたのか

- SSLBump（MITM）は **1 通信につき 1 回しか成立しない**
- 再度 MITM を行うと TLS が破綻するため、
  「入口でのみ復号し、中継では復号しない」設計が必須となる

これは商用プロキシ製品では前提として隠蔽されている部分ですが、  
OSS 環境では **実際に失敗を経験しながら理解・設計する必要がありました**。

---

#### この設計で再現している商用的な考え方

- 入口装置で復号
- 中継装置は非復号
- 出口装置は制御・記録に専念

結果として、  
**商用プロキシで「自然に成立している構造」を OSS で言語化・再構築**しています。

---

### 5-2. プロキシ間通信を stunnel で TLS 化した理由

商用プロキシでは前提とされている **プロキシ間通信の暗号化** を、  
OSS 環境では Squid 単体で実現できないため、  
暗号化責務を stunnel に分離しました。

proxy1
→ proxy1-stunnel
→ proxy2-stunnel
→ proxy2
→ proxy2-stunnel
→ proxy3-stunnel
→ proxy3


![stunnel-flow](./images/stunnel-flow.png)

この構成により、

- 中継区間の盗聴リスクを排除
- 将来のマルチホスト化にも耐える
- 商用と同等の「通信境界」を明示的に再現

しています。

---

### 5-3. WSL2 ミラーモードを採用した理由

Kerberos・WPAD・DNS は  
**L2 が分断されると成立しない設計** になっています。

NAT モードではそれらが破綻するため、  
商用ネットワークと同等の前提条件を作る目的で  
WSL2 の mirrored モードを採用しました。

これにより、

- Windows / WSL2 / VMware を同一 L2 に統合
- Kerberos・LDAP・WPAD を実環境同様に検証
- エンドツーエンドでの通信再現が可能

となっています。

---

### 5-4. PAC / WPAD による経路制御の再現

商用環境では一般的な、

- 利用者や宛先に応じた経路切替
- 障害時の迂回経路

を、PAC / WPAD を用いて再現しています。

- PAC：通信内容に応じて proxy1 / proxy2 / 直通を選択
- WPAD：PAC URL の自動配布

![pac-wpad-flow](./images/pac-wpad-flow.png)

---

## 6. 苦労した点と解決アプローチ

本章では、構築の過程で直面した課題と、  
**商用環境では暗黙となっている前提を OSS 環境で整理・解決した内容**をまとめています。

---

### 6-1. stunnel が「構造的に必須」であると理解するまで

初期の疑問：

> 「Docker の bridge 内通信であれば、平文でも問題ないのでは？」

試行錯誤の結果、以下の点から **設計として平文を許容すべきではない** と判断しました。

- コンテナ内部でも root 権限取得時の盗聴リスクは残る
- Squid は復号後の HTTP を再暗号化できない
- 商用プロキシでは中継 TLS が前提となっている

これらを踏まえ、  
**プロキシ間の暗号化責務を stunnel に分離する構成** に行き着きました。

---

### 6-2. 多段 SSLBump が成立しない理由の理解

一度は、以下のような構成を試しました。

proxy1 (SSLBump) → proxy2 (SSLBump) → proxy3


しかし、この構成では HTTPS 通信が成立しませんでした。

原因は、

- proxy1 が MITM を行った時点で  
  クライアント証明書の文脈が失われる
- proxy2 で再度 MITM を試みると  
  TLS ハンドシェイクが成立しない

という **HTTPS の前提仕様** にありました。

この失敗を通じて、  
**SSLBump は 1 通信につき 1 回しか成立しない** という制約を実感として理解しました。

---

### 6-3. 認証基盤（AD / LDAP / Kerberos）のブラックボックスを解消

認証基盤をゼロから構築することで、  
これまでブラックボックスだった挙動を段階的に切り分けて理解できるようになりました。

- SPN / keytab がどのタイミングで使われるか
- CN / SAN の不一致による認証失敗
- Kerberos が時刻同期に強く依存する理由
- LDAP の階層構造と検索範囲
- 認証失敗が発生するレイヤの特定方法

![kerberos-flow](./images/kerberos-flow.png)

---

## 7. ログ・監視（Observability）

| 種類 | 内容 |
|------|------|
| アクセスログ | Squid → Loki / Graylog |
| 接続ログ | stunnel / Squid / DNS / Kerberos |
| 監視 | Zabbix（TLS-PSK + Sidecar Agent） |
| 可視化 | Grafana / Kibana |

**理解が深まったポイント：**

- 多段プロキシの遅延比較
- ICAP スキャン遅延の特定
- PAC 経路差異のログ分析
- Kerberos 認証失敗の根本原因特定

---

## 8. 自動化（DevOps 的アプローチ）

作成したスクリプト：

- `multiproxy_health_all.sh`
- 証明書更新自動化
- Zabbix オートプロビジョニング
- ICAP 動作自動テスト
- ボリューム初期化スクリプト

![healthcheck-output](./images/healthcheck-output.png)

---

## 9. 学習成果

- SSLBump の技術的限界と復号範囲設計
- TLS 再暗号化における役割分担
- PAC/WPAD の企業ネットワーク重要性
- Kerberos の内部処理
- 時系列ログ分析
- 再現性・自動化の本質的価値

---

## 10. まとめ（企業が見たい“強み”）

このプロジェクトは単なる構築練習ではなく、  
**“商用品質のアーキテクチャを OSS で再構築したエンジニアリング成果”**です。

### 得られた能力
- レイヤを跨いだ通信の理解
- OSS で商用品質を再現できる構築力
- トラブルを再現し理解する問題分析能力
- 自動化・標準化・再現性を重視する SRE 思考

---

## 10. 画像テンプレ（必要なら追加で貼れる）

```md
![architecture](./images/architecture.png)
![stunnel-flow](./images/stunnel-flow.png)
![pac-wpad-flow](./images/pac-wpad-flow.png)
![kerberos-flow](./images/kerberos-flow.png)
![proxy3-traffic-log](./images/proxy3-traffic-log.png)
![healthcheck-output](./images/healthcheck-output.png)
![docker-network](./images/docker-network.png)

## 11. 今後の展望（Next Steps）

本構成は検証・理解を目的としたオンプレミス／ローカル環境での再現を主眼としていますが、  
今後は以下の観点で発展させていくことを想定しています。

---

### 11-1. AWS 環境への移行

現在の構成をベースに、AWS 上での再構築を検討しています。

- EC2 / ALB / NLB を用いたプロキシ配置
- VPC / Subnet / Security Group を含めたネットワーク設計
- オンプレミス前提だった DNS / 認証 / 通信境界の再整理

ローカル環境で明示的に分解した設計要素を、  
**クラウド環境にどう適用・再設計するか**を検証対象とします。

---

### 11-2. SDD を用いた AI 駆動開発との掛け合わせ

仕様駆動開発（SDD）を取り入れ、  
設計・設定・検証を **AI と協調しながら進める開発スタイル**の検証を行います。

- 設計意図を仕様として明文化
- 仕様をもとにした設定生成・レビュー支援
- 変更時の影響範囲整理や再設計支援

インフラ構成を「人の記憶」ではなく  
**仕様として管理するアプローチ**への発展を目指します。

---

### 11-3. 監視項目のチューニング

現在は基本的な死活・性能監視を中心としていますが、  
今後は以下のような観点で監視の精度を高めていく予定です。

- 経路別レイテンシ・エラー率の可視化
- SSLBump / 認証失敗の兆候検知
- ログとメトリクスを組み合わせた原因特定

「見える」だけでなく  
**判断に使える監視**を目標とします。

---

### 11-4. 冗長化構成の検討

現状は設計理解を優先した単一構成ですが、  
将来的には冗長化も検証対象とします。

- Proxy / stunnel の冗長化
- フェイルオーバー時の挙動確認
- PAC / DNS と連携した経路切替

これにより、  
**可用性を含めた商用運用に近い検証**へ発展させる予定です。
