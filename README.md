# 多段フォワードプロキシシステムの構築ポートフォリオ

多段フォワードプロキシシステムの構築ポートフォリオです。  
設計意図から、実際に動作していることを示す検証エビデンスまで整理しています。

---

## ① 設計意図・全体構成

- 多段プロキシ構成の設計背景
- 経路制御（PAC）、復号（SSLBump）、中継TLS（stunnel）の役割分離
- 認証（AD / LDAP / Kerberos）およびログ基盤の位置付け

▶ https://gan2z.github.io/multiproxy-oss-portfolio/

---

## ② 構築・検証（動作証跡）

実際にシステムが動作していることを、スクリーンショットやログで確認できます。

- **Verification**  
  全体稼働・経路差・復号点・ログ追跡の要点をまとめた確認用ページ

▶ https://gan2z.github.io/multiproxy-oss-portfolio/verification.html  
