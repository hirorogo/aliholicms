# MediaCMS Webクライアント（デモ）

### **要件**

- nodejs: バージョン >= 14.17.0

---

### **インストール**

    npm install

---

### **開発**

    npm run start

ブラウザで開く：[http://localhost:8088](http://localhost:8088)

---

### **ビルド**

    npm run dist

フォルダ "**_frontend/dist_**" を生成します。

"**_frontend/dist/static_**" からフォルダとファイルを "**_static_**" にコピーします。

---

### テストスクリプト

#### test

すべてのユニットテストを一度実行します。

```sh
npm run test
```

#### test-watch

開発用にウォッチモードでテストを実行します。

```sh
npm run test-watch
```

#### test-coverage

`./coverage`フォルダでカバレッジレポート付きでテストを実行します。

```sh
npm run test-coverage
```

#### test-coverage-watch

ウォッチモードでカバレッジ付きでテストを実行します。

```sh
npm run test-coverage-watch
```
