# 履歴

## 3.0.0

### 機能
- Python/Djangoの要件とDockerfileを更新して、最新のPython 3.11を使用 - https://github.com/mediacms-io/mediacms/pull/826/files。このアップデートには、既存の（新規ではない）インストールでいくつかの手動手順が必要です。シングルサーバーまたはDocker Composeインストールのいずれかについて、[管理者ドキュメント](https://github.com/mediacms-io/mediacms/blob/main/docs/admins_docs.md#2-server-installation)の更新セクションを確認してください
- Docker ComposeでPostgreSQLをアップグレード - https://github.com/mediacms-io/mediacms/pull/749

### 修正
- HLS用のビデオプレーヤーオプション - https://github.com/mediacms-io/mediacms/pull/832
- AVIビデオが正しくビデオとして認識されない - https://github.com/mediacms-io/mediacms/pull/833

## 2.1.0

### 修正
- uwsgiのbuffer-sizeパラメータを増やす。これにより、大きなヘッダーを持つuwsgiのエラーを防ぎます - [#5b60](https://github.com/mediacms-io/mediacms/commit/5b601698a41ad97f08c1830e14b1c18f73ab8315)
- コメントの問題を修正。これらはトラッカーで報告されていませんでしたが、メディアファイル（ビデオ以外だけでなくビデオも）にコメントが表示されないことは確実です。残念ながら、これはコメントのタイムスタンプ+コメントのメンションで行われた作業を元に戻します。詳細はPR [#802](https://github.com/mediacms-io/mediacms/pull/802)を参照してください

### 機能
- タグに英語のアルファベット以外の文字も含めることを許可 [#801](https://github.com/mediacms-io/mediacms/pull/801)
- シンプルなCookie同意コードを追加 [#799](https://github.com/mediacms-io/mediacms/pull/799)
- グローバルログイン必須時にパスワードリセット&メール確認ページを許可 [#790](https://github.com/mediacms-io/mediacms/pull/790)
- 検索APIにapi_urlフィールドを追加 [#692](https://github.com/mediacms-io/mediacms/pull/692)