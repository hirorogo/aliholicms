# 開発者ドキュメント

## 目次
- [1. ようこそ](#1-ようこそ)
- [2. システムアーキテクチャ](#2-システムアーキテクチャ)
- [3. APIドキュメント](#3-apiドキュメント)
- [4. 貢献方法](#4-貢献方法)
- [5. Dockerでの作業のヒント](#5-dockerでの作業のヒント)
- [6. 自動テストでの作業](#6-自動テストでの作業)
- [7. 動画のトランスコード方法](#7-動画のトランスコード方法)

## 1. ようこそ
このページはMediaCMSの開発者向けに作成され、関連情報が含まれています。

## 2. システムアーキテクチャ
執筆予定

## 3. APIドキュメント
APIはSwaggerを使用してドキュメント化されています - http://your_installation/swagger でご覧ください - 例：https://demo.mediacms.io/swagger/
このページでは、認証されたアクションを実行するためにログインできます - ログインしている場合はセッションも使用されます。


Python requestsライブラリでの作業例：

```
import requests

auth = ('user' ,'password')
upload_url = "https://domain/api/v1/media"
title = 'x title'
description = 'x description'
media_file = '/tmp/file.mp4'

requests.post(
    url=upload_url,
    files={'media_file': open(media_file,'rb')},
    data={'title': title, 'description': description},
    auth=auth
)
```

## 4. 貢献方法
PRを送信する前に、コードが適切にフォーマットされていることを確認してください。そのために、`pre-commit install`を使用してpre-commitフックをインストールし、`pre-commit run --all`を実行してコミット前にすべてを修正してください。このpre-commitは、コードをコミットするたびにコードのlintをチェックします。

このリポジトリに貢献したい場合は、[行動規範ページ](../CODE_OF_CONDUCT.md)をご覧ください


## 5. Dockerでの作業のヒント

Dockerインストールを実行するには、Docker + Docker composeのインストール手順（docs/Docker_Compose.md）に従い、その後docker-compose-dev.yamlをビルド/起動します。これにより、他のすべてのコンテナ（ポート80のDjango Webアプリケーションを含む）の上でポート8088でフロントエンドアプリケーションが実行されます

```
docker compose -f docker-compose-dev.yaml build
docker compose -f docker-compose-dev.yaml up
```

インストールプロセス中に`admin`ユーザーが作成されます。その属性は`docker-compose-dev.yaml`で定義されています：
```
ADMIN_USER: 'admin'
ADMIN_PASSWORD: 'admin'
ADMIN_EMAIL: 'admin@localhost'
```

### フロントエンドアプリケーションの変更
例えば`frontend/src/static/js/pages/HomePage.tsx`を変更すると、開発アプリケーションが数秒で更新され（ホットリロード）、変更を確認できます。満足したら、次を実行できます

```
docker compose -f docker-compose-dev.yaml exec -T frontend npm run dist
```

そして、nginxを介して提供されるアプリケーションで変更が表示されるようにするには、

```
cp -r frontend/dist/static/* static/
```

POST呼び出し：開発サーバーを介して実行できません。通常のアプリケーション（ポート80）を介して実行し、ポート8088の開発アプリケーションで変更を確認する必要があります。
異なる場合は、`frontend/.env`でURLが設定されていることを確認してください


メディアページ：メインアプリケーション（nginx/ポート80）を介してコンテンツをアップロードし、ページmedia.htmlのIDを使用する必要があります。例：`http://localhost:8088/media.html?m=nc9rotyWP`

一部のページが機能するために解決すべきCORSの問題もあります。例えば、コメント管理ページ

```
http://localhost:8088/manage-media.html manage_media
```

### バックエンドアプリケーションの変更
djangoアプリケーションに変更を加えた後（例えば`files/forms.py`を変更）、変更を確認するにはwebコンテナを再起動する必要があります

```
docker compose -f docker-compose-dev.yaml restart web
```

## 動画のトランスコード方法

オリジナルファイルはアプリケーションサーバーにアップロードされ、FileFieldsとして保存されます。

ファイルが動画で、期間が一定の数値（設定で定義、おそらく4分）より長い場合、チャンクにも分割されるため、すべての有効なEncodeProfilesに対して、チャンクごとに1つのEncodeオブジェクトが作成されます。

その後、ワーカーはEncodeオブジェクトの取得を開始し、チャンクをトランスコードします。チャンクが正しくトランスコードされると、オリジナルファイル（小さなチャンク）がトランスコードされたファイルに置き換えられ、Encodeオブジェクトのステータスが「success」としてマークされます。


original.mp4（1G、720px）--> Encode1（100MB、240px、chunk=True）、Encode2（100MB、240px、chunk=True）...EncodeXX（100MB、720px、chunk=True）---> すべてのEncodeオブジェクトが解像度に対してsuccessになると、original_resolution.mp4ファイルに連結され、これがEncodeオブジェクトとして保存されます（chunk=False）。これがダウンロード可能になります。

明らかに、Encodeオブジェクトは、最終的に提供されるエンコードされたファイル（chunk=False、status='success'）を保存するために使用されますが、トランスコード中のファイル（chunk=True、status='pending/etc'）も保存します

（括弧を開く）
APIのみを介して通信する実験的な小さなサービス（現在リポジトリにコミットされていない）もあり、a）実行するタスクを取得し、b）結果を返します。つまり、リクエストを行い、ffmpegコマンドとファイルを受信し、ffmpegコマンドを実行し、結果を返します。このメカニズムを、より多くのサーバー/cpuを介して既存の動画を移行するために多くのインストールで使用しており、1つの問題（サーバーから削除する必要がある一時ファイルがあった（定期的なタスクを介して、それほど大きな問題ではない））を除いて機能しました
（括弧を閉じる）


Encodeオブジェクトがsuccessとしてマークされ、chunk=Falseであり、したがってダウンロード/ストリームに利用できる場合、タスクが開始され、ファイルのHLSバージョン（1 mp4 --> 複数の小さな.tsチャンク）が保存されます。これがFILES_Cになります

このメカニズムにより、同じファイルシステムにアクセスできるワーカー（localhostまたは共有ネットワークファイルシステム（NFS/EFSなど）を介して）が同時に作業し、結果を生成できます。

## 6. 自動テストでの作業

この手順は、Dockerインストールを使用していることを前提としています

1. docker-composeを起動

```
docker compose up
```

2. webコンテナに`requirements-dev.txt`の要件をインストールします（このためにwebコンテナを使用します）

```
docker compose exec -T web pip install -r requirements-dev.txt
```

3. 既存のテストを実行できます

```
docker compose exec --env TESTING=True -T web pytest
```

`TESTING=True`は、Djangoがこれがテスト環境であることを認識するために渡されます（たとえば、Celeryはpytestの場合は起動されないため、Celeryタスクをバックグラウンドタスクとしてではなく関数として実行します）


4. パスを指定して、単一のテストを試すことができます。例：

```
docker compose exec --env TESTING=True -T web pytest tests/test_fixtures.py
```

5. カバレッジも確認できます

```
docker compose exec --env TESTING=True -T web pytest --cov=. --cov-report=html
```

そしてもちろん...それを増やすのを手伝っていただければ大歓迎です ;)
