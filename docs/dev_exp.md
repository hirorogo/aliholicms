# 開発者エクスペリエンス
より良い開発者エクスペリエンスを提供し、それをドキュメント化する取り組みが進行中です。

## Dockerでローカル開発する方法
まず、最新バージョンの[Docker](https://docs.docker.com/get-docker/)と[Docker Compose](https://docs.docker.com/compose/install/)をインストールします。

次に`docker compose -f docker-compose-dev.yaml up`を実行します

```
user@user:~/mediacms$ docker compose -f docker-compose-dev.yaml up
```

数分でアプリがhttp://localhostで利用可能になります。admin/adminでログインします

### docker-compose-dev.yamlは何をしますか？
バックエンドとフロントエンドに使用される2つのイメージをビルドします。

* バックエンド: `mediacms/mediacms-dev:latest`
* フロントエンド: `frontend`

そして、MediaCMSに必要なすべてのサービス（非同期タスクのためのCelery/Redis、PostgreSQLデータベース、Django、React）を起動します

Djangoの場合、docker-compose.yamlによって生成されるイメージからの変更は次のとおりです：

* Djangoは`python manage.py runserver`でデバッグモードで実行されます
* uwsgiとnginxは実行されません
* Djangoはデバッグツールバー付きのデバッグモードで実行されます
* 静的ファイル（js/css）はstatic/フォルダからロードされます
* corsheadersがインストールされ、すべてのオリジンを許可するように設定されています

Reactの場合、frontendフォルダで`npm start`を実行し、開発サーバーを起動します。
http://localhost:8088/で確認してください

### Djangoで開発する方法
Djangoはhttp://localhostで起動し、自動的にリロードされます。Pythonコードに変更を加えると、Djangoが更新されます。

Djangoがエラー（SyntaxErrorなど、コード編集中）によって壊れた場合、再起動する必要があるかもしれません

```
docker compose -f docker-compose-dev.yaml restart web
```



### Reactで開発する方法
Reactはhttp://localhost:8088/で起動し、コードはfrontend/にあります。そこで変更を加えると、ページに即座に反映されます。ReactはDjangoからデータをロードしており、Djangoが提供できるようにビルドする必要があることに注意してください。

### フロントエンドの変更

Reactが追加される方法は、通常のSPAプロジェクトよりも複雑です。これは、ReactがDjangoテンプレートによってロードされるライブラリとして使用されるため、スタンドアロンプロジェクトではなく、ルートなどを処理していないためです。

考慮すべき2つのディレクトリは次のとおりです：
* frontend/src 、Reactファイル用
* templates/、Djangoテンプレート用。

Djangoは非常に直感的な階層的テンプレートシステム（https://docs.djangoproject.com/en/4.2/ref/templates/）を使用しており、基本テンプレートはtemplates/root.htmlで、他のすべてのテンプレートはそれを拡張しています。

Reactはdjangoテンプレートを通じて呼び出されます。例えば、templates/cms/media.htmlはjs/media.jsをロードします

Reactコードに変更を加えるには、frontend/srcのコードを編集し、http://localhost:8088/でその効果を確認します。準備ができたら、ビルドしてDjangoの静的フォルダにコピーすると、Djangoによって提供されます。

### フロントエンドでの開発ワークフロー
1. frontend/src/ファイルを編集
2. http://localhost:8088/で変更を確認
3. `docker compose -f docker-compose-dev.yaml exec frontend npm run dist`でフロントエンドをビルド
4. `cp -r frontend/dist/static/* static/`で静的ファイルをDjangoの静的フォルダにコピー
5. Djangoを再起動 - `docker compose -f docker-compose-dev.yaml restart web` して新しい静的ファイルを使用
6. 変更をコミット

### ヘルパーコマンド
ヘルパーコマンドを提供する取り組みが進行中です。Makefileで何がサポートされているかを確認してください。例：

webコンテナへのbash：

```
user@user:~/mediacms$ make admin-shell
root@ca8c1096726b:/home/mediacms.io/mediacms# ./manage.py shell
```

フロントエンドのビルド：

```
user@user:~/mediacms$ make build-frontend
docker compose -f docker-compose-dev.yaml exec frontend npm run dist

> mediacms-frontend@0.9.1 dist /home/mediacms.io/mediacms/frontend
> mediacms-scripts rimraf ./dist && mediacms-scripts build --config=./config/mediacms.config.js --env=dist
...
```