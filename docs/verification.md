
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

本ドキュメントは、ポートフォリオ本文（README）で主張している内容を、**スクリーンショット／ログ／図で裏付ける**ための取得計画です。  
「何を証明するか」「どこで何を実行するか」「何を撮るか」を試験ID単位で整理します。

> 方針：README は“設計思想と構造”、本ドキュメントは“動作する証拠”を提示する

---

## 1. README章と検証観点（試験ID）の対応

| README章 | 試験ID | 何を証明するか（スクショ／ログ） | 想定ファイル名 |
|---|---|---|---|
| 1. プロジェクト概要 | P1-1, P1-2 | 全体が稼働している俯瞰（ヘルスチェック＋コンテナ稼働） | `healthcheck-output.png`, `docker-ps.png` |
| 2. システムアーキテクチャ | P2-1 | アーキ図と稼働コンポーネントが 1:1 で対応 | `architecture.png`, `docker-ps-arch-match.png` |
| 4. 技術スタック | P3-1, P3-2 | OSS の採用理由・役割がバージョン含めて説明できる | `version-matrix.png`, `squid-version.png` |
| 5-1. SSLBump（経路別に復号） | P4-1, P4-2, P4-3 | 経路①は Proxy1 が復号、経路②（DIRECT）は Proxy2 が復号 | `sslbump-cert.png`, `sslbump-proxy1-log.png`, `sslbump-proxy2-log.png` |
| 5-2. stunnel（中継TLS） | P5-1, P5-2 | Proxy間が stunnel で TLS 化されている（商用の中継暗号化相当を分離実装） | `stunnel-flow.png`, `stunnel-sclient.png`, `stunnel-log.png` |
| 5-3. WSL2 mirrored | P6-1, P6-2 | 同一L2前提（Kerberos/WPAD/DNS）が成立する環境設計 | `wsl2-mirrored-ip.png`, `ad-domain-join.png` |
| 5-4. PAC/WPAD | P7-1, P7-2 | PAC 取得・経路切替（通常/Proxy2-DIRECT）が再現できている | `wpad-dat.png`, `pac-flow-normal-vs-direct.png` |
| 6-1. stunnel導入に至る過程 | P8-1（任意） | “なぜ必要か”を脅威モデル＋制約（Squid）で説明できる | `no-stunnel-note.png` |
| 6-2. 多段SSLBump失敗 | P9-1（任意） | “復号は1回だけ”の制約を失敗から理解し設計修正 | `double-bump-error.png` |
| 6-3. 認証（AD/LDAP/Kerberos） | P10-1〜P10-3 | 認証フローを CLI とログで追跡できる | `kerberos-flow.png`, `ldapwhoami.png`, `ldapsearch-testuser1.png`, `proxy-auth-log.png` |
| 7. ログ・監視 | P11-1〜P11-3 | Graylog/Loki/Zabbix/Grafana で追跡・可視化できる | `graylog-search.png`, `grafana-proxy-deny.png`, `zabbix-hosts.png` |
| 8. 自動化 | P12-1, P12-2 | スクリプトで再現性ある起動・検証・復旧ができる | `healthcheck-output.png`, `dnsmasq-pac-healthcheck.png` |
| 学習成果（強み） | P13-1 | 想定外挙動を調査して原因を言語化できる | `wpad-shortname-issue.png` |

> 任意（P8/P9）は「再現で壊れる」可能性があるため、**当時のログやメモが残っていればそれを採用**し、無理に再現しない方針。

---

## 2. 共通前提（撮影前チェック）

- multiproxy 配置：`/home/login00/multiproxy`
- WSL2 上で `docker compose ps` が概ね `Up`（必要に応じて `healthy`）
- Windows クライアントは AD ドメイン参加済み & PAC 自動取得済み
- 画像は原則 `images/` に格納（README から参照）

---

## P1：プロジェクト概要「全体が動いている」証拠

### 主張（面接で刺さる一言）
多段プロキシ／認証／ログ／監視が **統合された状態で稼働**しており、復旧・確認もスクリプトで再現できる。

### 撮るもの
- ヘルスチェック結果
- コンテナ稼働一覧（proxy1〜3、ldap、pac、graylog、loki、grafana、zabbix など）

### 手順

#### P1-1. Health Check 出力

WSL2 で実行：

```bash
cd /home/login00/multiproxy
./scripts/multiproxy_health_all.sh
```

- 画面上で `[OK]` が一通り揃っていることを確認してスクリーンショットを取得  
  → `healthcheck-output.png`

---

#### P1-2. コンテナ一覧

WSL2 で実行：

```bash
cd /home/login00/multiproxy
docker compose ps
```

- 主要コンテナが `Up` になっている画面をスクリーンショット  
  → `docker-ps.png`

---

### P2：アーキテクチャ図と稼働コンポーネントの一致

**主張：**  
設計したアーキテクチャと、実際に稼働しているコンポーネントが  
**1:1 で対応していることを示す。**

**撮るもの：**
- `architecture.png`
- `docker-ps.png` の対応箇所トリミング  
  → `docker-ps-arch-match.png`

---

#### P2-1. アーキ図とコンテナ対応

1. draw.io / Excalidraw 等でアーキテクチャ図を作成  
   → `architecture.png`

2. `docker-ps.png` の中から、  
   Proxy / LDAP / Graylog / Loki / Zabbix 等が写っている部分をトリミング  
   → `docker-ps-arch-match.png`

### P3：技術スタック（バージョンと役割）の証拠

**主張：**  
OSS を「なんとなく使う」のではなく、  
**役割・制約・バージョンを把握した上で採用・構成している。**

---

#### 手順

##### P3-1. バージョン表

- 作成済みのバージョン一覧（Markdown / Excel）を表示
- 一覧全体が確認できる画面をスクリーンショット  
  → `version-matrix.png`

---

##### P3-2. 代表例（Squid）

WSL2 で実行：

```bash
docker exec -it proxy1 squid -v
```

- Squid のバージョン（例：5.7）が表示されている画面をスクリーンショット  
  → `squid-version.png`

---

### P4：SSLBump の適用範囲（経路別）

**主張（README 5-1 の裏付け）：**

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

##### P4-1. HTTPS アクセス & 証明書

- Windows クライアントで HTTPS サイト（例：`https://cloudflare.com`）にアクセス
- ブラウザの証明書表示を開き、  
  **発行者が internal CA になっている画面**をスクリーンショット  
  → `sslbump-cert.png`

---

##### P4-2. 経路①（通常）で Proxy1 が復号しているログ

WSL2 で実行（例：cloudflare.com）：

```bash
docker exec -it proxy1 bash -lc \
  "grep -a 'cloudflare.com' /var/log/squid/access.log | tail -n 20"
```

- `ssl_bump` を伴うログが Proxy1 に出ている部分をスクリーンショット  
  → `sslbump-proxy1-log.png`

---

##### P4-3. 経路②（DIRECT）で Proxy2 が復号しているログ

DIRECT 対象サイト（例：wikipedia.org）へアクセス後、  
Proxy2 の 3131 ポート側ログを確認：

```bash
docker exec -it proxy2 bash -lc \
  "grep -a 'wikipedia.org' /var/log/squid/access_3131.log | tail -n 20"
```

- Proxy2（3131）で復号が行われていることが分かるログをスクリーンショット  
  → `sslbump-proxy2-log.png`

### P5：stunnel によるプロキシ間 TLS（中継暗号化）の証拠

**主張（README 5-2 の裏付け）：**  
商用プロキシでは機器内機能として提供される  
**「プロキシ間通信の暗号化（中継 TLS）」** を、  
Squid 単体では実現できないため、**stunnel によって分離実装**している。

---

#### 手順

##### P5-1. openssl s_client による TLS 確認

WSL2 で実行：

```bash
docker exec -it proxy2-3-stunnel sh -lc \
  'openssl s_client -connect proxy3-stunnel:4433 \
   -servername proxy3-stunnel \
   -brief </dev/null 2>&1 | head -n 15'
```

- `CONNECTION ESTABLISHED`
- `Protocol version: TLSv1.3` などが確認できる画面をスクリーンショット  
  → `stunnel-sclient.png`

---

##### P5-2. stunnel ログの確認

WSL2 で実行：

```bash
docker exec -it proxy1-stunnel sh -lc \
  "tail -n 80 /var/log/stunnel/stunnel.log"
```

- `SSL accepted` / `Connection established` 等が確認できる箇所をスクリーンショット  
  → `stunnel-log.png`

---

### P6：WSL2 mirrored mode & AD/DC 連携

**主張：**  
Kerberos / WPAD / DNS の前提となる **「同一 L2 セグメント」** を満たし、  
実環境（商用ネットワーク）と同等の条件で検証できている。

---

#### 手順

##### P6-1. IP アドレス比較（同一セグメント）

以下の 3 つを並べてスクリーンショット：

- Windows クライアント：  
  ```text
  ipconfig
  ```
- WSL2（Ubuntu）：  
  ```bash
  ip a
  ```
- AD/DC VM：  
  ```bash
  ip a
  ```

- すべてが同一セグメント（例：`192.168.11.0/24`）であることが分かる画  
  → `wsl2-mirrored-ip.png`

---

##### P6-2. ドメイン参加の証拠

- Windows ログイン画面で  
  **AD ユーザ（例：`AD\testuser1`）が選択できる状態**をスクリーンショット  
  → `ad-domain-join.png`

---

### P7：PAC / WPAD による経路制御（通常 vs DIRECT）

**主張：**  
商用環境で一般的な  
**「宛先・条件に応じたプロキシ経路切替」** を、PAC / WPAD で再現している。

---

#### 手順

##### P7-1. PAC の内容確認

クライアントブラウザで以下にアクセス：

```text
http://wpad.ad.lan:8080/wpad.dat
```

- PAC 内に以下が記載されている箇所をスクリーンショット：
  - `PROXY proxy1.ad.lan:3128`
  - `PROXY proxy2.ad.lan:3129`
  - `PROXY proxy2.ad.lan:3131`
  - `DIRECT`
  
  → `wpad-dat.png`

---

##### P7-2. 経路差のログ確認（NORMAL vs P2-DIRECT）

NORMAL 対象サイトと DIRECT 対象サイトへアクセス後、  
各 Proxy のログを比較する。

###### NORMAL（例：cloudflare.com）

```bash
docker exec -it proxy1 bash -lc \
  "grep -a 'cloudflare.com' /var/log/squid/access.log | tail -n 10"

docker exec -it proxy2 bash -lc \
  "grep -a 'cloudflare.com' /var/log/squid/access_3129.log | tail -n 10"

docker exec -it proxy3 bash -lc \
  "grep -a 'cloudflare.com' /var/log/squid/access_main.log | tail -n 10"
```

###### P2-DIRECT（例：wikipedia.org）

```bash
docker exec -it proxy1 bash -lc \
  "grep -a 'wikipedia.org' /var/log/squid/access.log | tail -n 10"

docker exec -it proxy2 bash -lc \
  "grep -a 'wikipedia.org' /var/log/squid/access_3131.log | tail -n 10"

docker exec -it proxy3 bash -lc \
  "grep -a 'wikipedia.org' /var/log/squid/access_direct.log | tail -n 10"
```

- NORMAL：`proxy1 / proxy2 / proxy3` すべてにログが出る  
- DIRECT：`proxy1` には出ず、`proxy2(3131)` と `proxy3(3132)` のみに出る  

この差が分かる画面をスクリーンショット  
→ `pac-flow-normal-vs-direct.png`



### P8：stunnel が必要だと理解するに至った過程（任意）

**主張：**  
商用プロキシでは暗黙的に提供される  
**「中継区間の暗号化」** を、OSS の制約と脅威モデルから分解し、  
自ら再設計できることを示す。

---

#### 取り方（おすすめ）

- 当時の設計メモや判断理由を README 5-2 から再掲
- **意図的に壊す再現テストは行わない**
  - 再現が必要な場合は別ブランチで実施
- テキスト主体の説明画像でも可

→ `no-stunnel-note.png`（メモ／設計判断のスクリーンショット）

---

### P9：多段 SSLBump が失敗したログ（任意）

**主張：**  
**「HTTPS 復号は 1 通信につき 1 回のみ」** という制約を、  
実際の失敗を通じて理解し、  
**経路別に復号ポイントを分離する設計**へ落とし込んだ。

---

#### 取り方

- 当時保存していたエラーログ（再現不要）
  - 例：`TCP_MISS/503`、TLS handshake error 等
- 「proxy2 でも bump しようとして失敗した」ことが分かる内容

→ `double-bump-error.png`

※ 再現は必須ではなく、**当時のログ＋設計改善の説明**で十分。

---

### P10：AD / LDAP / Kerberos のフロー可視化

**主張：**  
認証をブラックボックスにせず、  
**CLI とログを使って認証フローを段階的に切り分けられる**。

---

#### 手順

##### P10-1. ldapwhoami（LDAP 疎通確認）

```bash
docker exec -it ldap sh -lc '
BIND_PW="$(cat /run/secrets/bind_pw)"
ldapwhoami -x -H ldap://ldap:389 \
  -D "CN=LDAP Proxy,OU=Service Accounts,DC=ad,DC=lan" \
  -w "$BIND_PW"
'
```

- `dn: CN=LDAP Proxy,...` が表示される画面をスクリーンショット  
  → `ldapwhoami.png`

---

##### P10-2. testuser1 の検索

```bash
docker exec -it ldap sh -lc '
BIND_PW="$(cat /run/secrets/bind_pw)"
ldapsearch -LLL -x -H ldap://ldap:389 \
  -D "CN=LDAP Proxy,OU=Service Accounts,DC=ad,DC=lan" \
  -w "$BIND_PW" \
  -b "OU=TestUsers,DC=ad,DC=lan" \
  "(sAMAccountName=testuser1)" dn sAMAccountName cn
'
```

- `dn: CN=testuser1,...` が確認できる画面  
  → `ldapsearch-testuser1.png`

---

##### P10-3. Proxy ログに認証ユーザが出ていることの確認

```bash
docker exec -it proxy1 sh -lc \
  "grep -a 'testuser1@AD.LAN' /var/log/squid/access.log | tail -n 10"
```

- ユーザ名付きアクセスログが確認できる画面  
  → `proxy-auth-log.png`

---

### P11：ログ・監視（Observability）

**主張：**  
ログ（Graylog / Loki）と監視（Zabbix）を組み合わせ、  
**障害の兆候検知と原因特定に当たりを付けられる**。

---

#### 手順

##### P11-1. Graylog 検索

- 検索例：  
  ```text
  TCP_DENIED AND facebook.com
  ```
- ログ一覧とフィールドが表示されている画面  
  → `graylog-search.png`

---

##### P11-2. Grafana（Loki）

- Explore → LogQL 例：

```logql
{container="proxy1"} |= "TCP_DENIED"
```

- 時系列グラフ＋ログ一覧の画面  
  → `grafana-proxy-deny.png`

---

##### P11-3. Zabbix

- Hosts 一覧がすべて Green  
  → `zabbix-hosts.png`
- proxy1 のグラフ（トラフィック / deny カウント）  
  → `zabbix-proxy1-graph.png`

---

### P12：自動化（再現性）

**主張：**  
手作業ではなく、  
**スクリプトで「壊しても戻せる・確認できる」状態**を作っている。

---

#### 手順

##### P12-1. dnsmasq + PAC ヘルスチェック

```bash
cd /home/login00/multiproxy/scripts
./dnsmasq_pac_healthcheck.sh
```

- WPAD 解決 / PAC GET / PROXY 行が確認できる画面  
  → `dnsmasq-pac-healthcheck.png`

---

##### P12-2. 全体ヘルスチェック（再掲）

- P1-1 の結果を再利用可  
  → `healthcheck-output.png`

---

### P13：想定外動作の切り分け（WPAD 短縮名問題）

**主張：**  
「なんか変」で終わらせず、  
**仕様と挙動を調べて原因を言語化できる**。

---

#### 手順（Windows クライアント）

PowerShell：

```powershell
nslookup wpad
ping wpad
Test-NetConnection wpad -Port 8080
```

ブラウザ：

```text
http://wpad.ad.lan:8080/wpad.dat
```

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
