# MediaCMS

[![GitHub license](https://img.shields.io/badge/License-AGPL%20v3-blue.svg)](https://raw.githubusercontent.com/mediacms-io/mediacms/main/LICENSE.txt)
[![Releases](https://img.shields.io/github/v/release/mediacms-io/mediacms?color=green)](https://github.com/mediacms-io/mediacms/releases/)
[![DockerHub](https://img.shields.io/docker/pulls/mediacms/mediacms)](https://hub.docker.com/r/mediacms/mediacms)



MediaCMSは、モダンで完全な機能を備えたオープンソースの動画およびメディアCMSです。メディアの閲覧と共有を行う最新のWebプラットフォームのニーズを満たすために開発されました。数分以内に小規模から中規模の動画およびメディアポータルを構築するために使用できます。

主にDjango + Reactのモダンなスタックを使用して構築されており、REST APIが含まれています。

デモは https://demo.mediacms.io でご覧いただけます。


## スクリーンショット

<p align="center">
    <img src="https://raw.githubusercontent.com/mediacms-io/mediacms/main/docs/images/index.jpg" width="340">
    <img src="https://raw.githubusercontent.com/mediacms-io/mediacms/main/docs/images/video.jpg" width="340">
    <img src="https://raw.githubusercontent.com/mediacms-io/mediacms/main/docs/images/embed.jpg" width="340">
</p>

## 機能
- **データの完全な管理**: 自分自身でホスティング可能！
- **モダンな技術**: Django/Python/Celery、React
- **複数の公開ワークフローのサポート**: パブリック、プライベート、非公開、カスタム
- **ロールベースアクセス制御（RBAC）**: RBACカテゴリを作成し、ユーザーをグループに接続してメディアへの閲覧/編集アクセスを管理
- **自動文字起こし**: ローカルで実行されるWhisperとの統合による機能
- **複数のメディアタイプのサポート**: 動画、音声、画像、PDF
- **複数のメディア分類オプション**: カテゴリ、タグ、カスタム
- **複数のメディア共有オプション**: ソーシャルメディア共有、動画埋め込みコード生成
- **ビデオトリマー**: 動画のトリミング、置換、新規保存、セグメント作成
- **SAMLサポート**: システムロールとグループへのマッピング追加機能付き
- **簡単なメディア検索**: ライブ検索機能による充実した検索
- **音声および動画コンテンツのプレイリスト**: プレイリストの作成、コンテンツの追加と並べ替え
- **レスポンシブデザイン**: ライトテーマとダークテーマを含む
- **高度なユーザー管理**: 自己登録、招待のみ、クローズドの選択が可能
- **設定可能なアクション**: ダウンロード、コメント追加、いいね、よくない、メディア報告の許可
- **設定オプション**: ロゴ、フォント、スタイリングの変更、ページの追加
- **拡張ビデオプレーヤー**: 複数の解像度と再生速度オプション付きのカスタマイズされたvideo.jsプレーヤー
- **複数のトランスコーディングプロファイル**: 複数の解像度（144p、240p、360p、480p、720p、1080p）と複数のプロファイル（h264、h265、vp9）のための適切なデフォルト設定
- **アダプティブビデオストリーミング**: HLSプロトコルによる実現
- **字幕/クローズドキャプション**: 多言語字幕ファイルのサポート
- **スケーラブルなトランスコーディング**: 優先順位によるトランスコーディング。リモートワーカーの実験的サポート
- **チャンク分割されたファイルアップロード**: コンテンツの一時停止/再開可能なアップロード
- **REST API**: Swaggerによるドキュメント化
- **翻訳**: CMSのほとんどが多数の言語に翻訳されています

## 利用例

- **大学、学校、教育機関。** 管理者と編集者が公開するコンテンツを管理し、学生は広告や無関係なコンテンツに気を取られることなく、ストリーミングまたはダウンロードを選択できます。
- **組織の機密コンテンツ。** コンテンツが機密性が高く、外部サイトにアップロードできない場合。
- **素晴らしいコミュニティの構築。** MediaCMSはカスタマイズ可能（URL、ロゴ、フォント、デザイン）なので、コミュニティ向けの高度にカスタマイズされた動画ポータルを作成できます！
- **個人ポータル。** お好みの方法でコンテンツを整理、分類、ホスティングできます。


## 理念

私たちは、コミュニティポータルを構築し、コラボレーションをサポートするために使用できる、高品質なオープンソースWebアプリケーションの必要性があると信じています。
MediaCMSには3つの目標があります：a) 最新システムに期待されるすべての機能を提供する、b) 簡単なインストールとメンテナンスを可能にする、c) 機能の簡単なカスタマイズと追加を可能にする。


## ライセンス

MediaCMSは[GNU Affero General Public License v3.0 license](LICENSE.txt)の下でリリースされています。
Copyright Markos Gogoulos.


## サポートと有料サービス

カスタムインストール、追加機能の開発、既存システムからの移行、レガシーシステムとの統合、トレーニング、サポートを提供しています。詳細については、[サービスページ](https://mediacms.io/#services/)をご覧ください。

### 商用ホスティング
**Elestio**

Elestioでワンクリックデプロイを使用してMediaCMSをデプロイできます。Elestioは収益分配を提供してMediaCMSをサポートしているので、以下をクリックしてMediaCMSをデプロイして使用してください。

[![Deploy on Elestio](https://elest.io/images/logos/deploy-to-elestio-btn.png)](https://elest.io/open-source/mediacms)

## ハードウェアの考慮事項

小規模から中規模のインストールで、毎日数時間の動画がアップロードされ、数百人のアクティブな日次ユーザーがコンテンツを閲覧する場合、最低4GB RAM / 2-4 CPUで問題ありません。毎日多くの時間の動画がアップロードされる大規模なインストールの場合は、より多くのCPUとRAMの追加を検討してください。

ディスクスペースに関しては、必要量を考慮してください。一般的な目安は、予想されるアップロード動画のサイズを3倍にすることです（システムはオリジナルバージョン、エンコードバージョン、HLSを保持するため）。したがって、毎日1Gの動画を受信し、すべてを保持する場合、1年間で1Tのディスクを検討する必要があります（1G * 3 * 365）。

Whisperによる自動文字起こしをサポートするには、より多くのCPUを検討してください。

## インストール / メンテナンス

MediaCMSを実行する方法は2つあります。Docker Composeを使用する方法と、必要なすべてのサービスをインストールおよび設定する自動化スクリプトを介してサーバーにインストールする方法です。関連ページを参照してください：

- [シングルサーバー](docs/admins_docs.md#2-server-installation)ページ
- [Docker Compose](docs/admins_docs.md#3-docker-installation)ページ

  完全なガイドは、ブログ記事[How to self-host and share your videos in 2021](https://medium.com/@MediaCMS.io/how-to-self-host-and-share-your-videos-in-2021-14067e3b291b)で見つけることができます。

## ドキュメント

* [ユーザードキュメント](docs/user_docs.md)ページ
* [管理者ドキュメント](docs/admins_docs.md)ページ
* [開発者ドキュメント](docs/developers_docs.md)ページ
* [設定](docs/admins_docs.md#5-configuration)ページ
* [トランスコーディング](docs/transcoding.md)ページ
* [開発者エクスペリエンス](docs/dev_exp.md)ページ
* [メディアパーミッション](docs/media_permissions.md)ページ


## 技術

このソフトウェアは、以下の素晴らしい技術リストを使用しています：Python、Django、Django Rest Framework、Celery、PostgreSQL、Redis、Nginx、uWSGI、React、Fine Uploader、video.js、FFMPEG、Bento4


## 利用組織
- **複数の大学** 教育動画のホスティング用
- **Cinemata** 非営利メディア、テクノロジー、カルチャー組織 - https://cinemata.org
- **Critical Commons** パブリックメディアアーカイブとフェアユース提唱ネットワーク - https://criticalcommons.org
- **American Association of Gynecologic Laparoscopists** - https://surgeryu.org/


## 貢献方法

プロジェクトが気に入った場合、以下のことができます
- カスタムインストール、トレーニング、サポート、メンテナンス作業のために私たちを雇用する
- 私たちを雇用することに興味のある他の人に推薦する
- MediaCMSに関するブログ記事/記事を書く
- プロジェクトについてソーシャルメディアで共有する
- イシューを開く、[ディスカッション](https://github.com/mediacms-io/mediacms/discussions)に参加する、バグを報告する、アイデアを提案する
- プロジェクトをどのように使用しているかを[Show and tell](https://github.com/mediacms-io/mediacms/discussions/categories/show-and-tell)で共有する
- プロジェクトにスターを付ける
- 機能を追加する、PRに取り組む、イシューを修正する！


## お問い合わせ

info@mediacms.io
