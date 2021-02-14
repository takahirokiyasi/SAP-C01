# Opsworks
ChefやPuppetなどのサーバー設定ツールを元にした`ミドルウェア`の管理のサービス  
アプリケーションのデプロイ・スケーリング・保守の自動化が可能  
主にEC2上のミドルウェアやアプリケーションの設定管理を担い、デプロイ、モニタリング、スケーリングを行う。
CloudformationでOpsWorksを構築することも可能

## 構造
### スタック
Opsworksのトップエンティティ
### レイヤー
アプリケーションサーバーやDBサーバーなど、特定の目的を果たすEC2インスタンスのセットを表すもの
#### Elastic Load Balancing Layer
通常のOpsWorksスタックのレイヤーとは少し異なり、ELBレイヤーを追加するとレイヤーのインスタンスに負荷分散される。
ELBを作成してELBレイヤーに紐づけることでアタッチ完了
configureイベントがトリガされる。
#### RDS サービス Layer
RDSインスタンスを作成してRDS サービス Layerに紐づけることでアタッチ完了
セキュリティグループがOpsworksで使用する特定のIPを許可するものをアタッチしないといけない

#### ECS クラスター　Layer
EC2で起動するECSのクラスター管理を楽にするもの

### インスタンス
アプリケーションを提供するためのEC2インスタンスのこと
複数のレイヤーに所属することができる
AWS OpsWorks スタックでサポートされているAMIである必要がある。

### App
さまざまなメタデータ(アプリケーションの名前や種類など)と、アプリケーションをサーバーインスタンスにデプロイするために必要な情報 (リポジトリURLなど)を保持する。

## CloudFormation + Opsworks
CloudFormation内でOpsWorksコンポーネント（スタック・レイヤー・インスタンス・アプリケーション）をモデリングし、それらをCloudformation スタックとしてプロビジョニングすることができる

## 自動ヒーリング
インスタンスが Amazon EC2 ヘルスチェックに合格した場合でも、スタック内にある異常なインスタンスまたは失敗したインスタンスを再起動します。スタックのレイヤー設定では、自動ヒーリングがデフォルトで有効化されています。自動ヒーリングが有効になっている場合、ポータルのダウンタイムを回避するために、失敗したEC2インスタンスは自動的に置き換えられます。

## Opsworksエージェント
発行された一連のコマンドを取得
レシピを実行する

## Opsworksスタックのライフサイクルイベント
- Setup
インスタンスの初期化・正常起動時
- Configure
スタックの状態が変化する時
- Deploy
- Undeploy
アプリケーションが削除された時
- shutdown
インスタンスが停止する45秒前

## AWS Opsworks for Chef Automate
完全マネージド型のChefサーバを数クリックで作成し、ワークフローの自動化を設定することができます。
また、インスタンス設定ではサブネットやセキュリティグループ、バックアップの時間などを指定することができるようになっているほか、Chefコミュニティで配布されているCookbookなどと互換性があるため、サンプルコードとして利用することができます。
Chef Automateと言うサービスがAWS上で使用できるので今までChef Automate使ってる場合は便利

# CodePipeline
※CodePipeline自体にはデプロイ機能はなくCodeDeployにある
## ユーザーによる承認アクションを入れる
パイプラインのステージに承認アクションを追加した個所でパイプラインの実行を停止することで、必要な IAMアクセス許可を持つユーザーによるアクションを承認または拒否する設定が可能。
`SNS`を使ってをメールであったりSQSに送信したり、Lambdaを実行できる
レビューするのためのURLを指定できたりもする。

# CodeCommit
## 他のレポジトリサービスからの移行
レポジトリをクローンし、CodeCommitにPushする

# CodeDeploy
## デプロイ設定
[CodeDeployのデプロイ設定](https://docs.aws.amazon.com/ja_jp/codedeploy/latest/userguide/deployment-configurations.html)
### Lambdaに対してのデプロイ設定
- Canary
一回目と二回目でデプロイを分ける。一回目は10%で、二回目で残りの90%をデプロイするとかできる
- 線形
毎回同じ間隔で、等しい増分だけ移行される
- All-at-once
一度に一気に新しいのに移行

# CodeStar
開発環境のスキャホールドと言えます。リポジトリ（CodeCommit）からビルド（CodeBuild）、そしてデプロイ（CodeDeploy）がCodePipelineで自動で作成される。デプロイするEC2インスタンスやBeanstalk環境も選択すれば自動で作成してくれる。
開発者は、最小限の情報を入力することで、AWS上で動くアプリケーション環境と最小限のソースコードを手に入れることができる。
VScodeなどの開発ツール連携してて、CI・CDの環境とつながることもできる。
料金は発生しない。作成したリソースに対して費用発生
CI/CD環境を初めてつくる時に便利

# Code Artifact
ソフトウェアパッケージを安全に保存、公開、共有できるようにするプライベートリポジトリサービス

# CloudFormation
アプリケーションレイヤ・Dataレイヤ・ネットワークレイヤでスタックを分けた方がいい

## Change Set （変更セット）
実行中のスタックに対する変更を適用するためのもの

## ドリフト
直接スタックをCloudFormationを介さずに変更してしまった場合の差分を検知するもの

## 削除保護
### スタックの削除保護
有効・無効でスタックを誤って削除されるのを防ぐ
### Stack Policy
特定のリソースに対して削除されないように保護されるもの
JSONドキュメントを書く、IAMみたいなやつを書く

### DeletionPolicy（削除保護）
スタックが削除されてもリソースを保持する設定。
削除時スナップショットをとる設定もある（EC2　ElastiCash RDSのみ）

## リソースインポート
手動で作成したスタックをCloudFormationのスタックにインポートして管理可能
リソースをスタックから切り離し別のスタック管理下に移動させることも可能

## Resource Tags プロパティ
Resource Tags プロパティを使用してリソースにタグを適用し、それらのリソースの識別や分類に役立てることができます。タグを適用できるのは、AWS CloudFormation がタグ付けをサポートしているリソースのみです。

## CreationPolicy 属性
CreationPolicy 属性をリソースに関連付けて、AWS CloudFormation が指定数の成功シグナルを受信するかまたはタイムアウト期間が超過するまでは、ステータスが作成完了にならないようにします。リソースにシグナルを送信するには、cfn-signal ヘルパースクリプトまたは SignalResource API を使用できる。
EC2とAuto ScalingリソースについてはWaitConditionよりCreation Policy推奨

## WaitCondition
次のような状況で待機条件を使用できます。
- スタックの作成の外部での設定アクションを使ったスタックリソース作成を調整するため
- 設定プロセスのステータスを追跡するため
EC2とAuto ScalingリソースについてはWaitConditionよりCreation Policy推奨

### ResourceSignalパラメーター
timeoutを指定することでタイムアウト時間を設定することができる。

### cfn-signal
cfn-signalヘルパースクリプトはAWS CloudFormationに信号を送り、Amazon EC2インスタンスが正常に作成または更新されたかどうかを示します。インスタンスにソフトウェアアプリケーションをインストールして設定する場合、それらのソフトウェアアプリケーションの準備ができたらAWS CloudFormationにシグナルを送ることができます。

## cfn-lint
VSCodeで使えるCloudFromationのLinter
エラーが起きるところを教えてくれる。

## SAMテンプレート
AWS :: Serverless :: Api　はSAMフレームワークのAPIゲートウェイリソース用に設計されている
AWS :: Serverless :: Functionは、Lambda関数、IAM実行ロール、イベントソースマッピングを作成するSAMリソース
AWS :: Serverless ::SimpleTableはDynamoDB
AWS :: Serverless :: Application はServerless Application Repositoryに存在するアプリケーションやSAMテンプレートからアプリケーションをデプロイ

### Transform
SAM構文を通常のCloudFromationに変換する方法を指定するもの（自分で定義することも可能）
CloudFormationのオプションの Transform セクションでは、CloudFormation テンプレートを処理するために使用するマクロを 1 つ以上指定します。Transform セクションではテンプレート内で 1 つ以上のマクロを宣言できます。マクロは、AWS CloudFormation によって、指定された順序で実行されます。変更セットを作成すると、AWS CloudFormation は処理されたテンプレートコンテンツを含む変更セットを生成します。その後、変更内容を確認して変更セットを実行できます。

AWS::Serverless Transformの設定において、使用する AWS SAM バージョンを指定することが必要
AWS::Serverless-2016-10-31

### Lambdaのコード
AWS::Lambda::Functionを利用して、Lambda 関数のデプロイパッケージをCloudFormationで参照します。すべてのランタイムに対して、Amazon S3 内のオブジェクトの場所を指定できます。(CodeUri)
CLIを利用すると自動でZipに固めて、指定したS3のバケットにアップロードし、CodeUriの値がS3のUriに変更される。
Node.js および Python 関数の場合で依存関係がない限りテンプレートにインラインで関数コードを指定できます。(CodeInline)

#### AutoPublishAlias
liveにすることで
lambdaの関数をバージョン管理することができる
DeploymentPreferenceを指定することでCodeDeployと自動で連携し、カナリアデプロイなどが可能

##### デプロイの種類
Canary10Percent30Minutes 10%を最新バージョンで実行し、30分後に全てが最新バージョンで実行される
All At Onece　全てのLambda関数の実行を最新バージョンで行う
Linear10PercentEvery10Minutes Lambda関数の実行のうち10%を最新バージョンで実行し、10分ごとに10%最新のバージョンを増やす 

## ヘルパースクリプト
スタック内のEC2のインスタンスの構築・変更を行うヘルパースクリプトが提供されている

## Dynamic Reference
パラメータストアとAWS Secret Managerに格納されたデータを動的に参照

### LambdaのEventプロパティ
#### typeプロバティ
何をトリガーにするか決めるところAPI gatewayとか選べる
S3をトリガーにする場合のみテンプレートでバケットを新規作成し、Ref関数で指定しないといけない。

#### properties
API Gatewayの場合pathやMethodを指定するだけでAPI Gatewayのリソースも勝手に作成される。(詳細な設定をする場合はRefで参照すればいい)
### SAM CLI
#### sam local invokeコマンド
ローカルでdocker上でLambda関数を動かすことができる
#### sam local generate-event
テスト用イベントの生成ができる

## StackSets
`複数のAWSアカウント、リージョン`に対しCloudFormationのスタックを作成できる機能
これにより複数アカウントにまたがる設定を一括して実行できたりしたり、
管理者アカウントは、スタックセットを作成する AWS アカウントのこと
ターゲットアカウントはスタックセットを使用する AWS アカウントのこと

### 手動でIAMロールを作成する場合(セルフマネージド型)
StackSetsを作成するアカウント（Administrationアカウント）から、他のアカウント（Targetアカウント）にスタックセットのスタックをリソースをためのIAMロールを作成することが事前準備として必要
Stackセットを作成するアカウントに`AWSCloudFormationStackSetAdministrationRole`を作成
Tagetアカウントに`AWSCloudFormationStackSetExecutionRole`を作成

### Organizationを使用する場合(サービスマネージド型)
マスターアカウントが管理アカウントになる
`AWSServiceRoleForCloudFormationStackSetsOrgAdmin`: 管理アカウントに作成されるロール
`AWSServiceRoleForCloudFormationStackSetsOrgMember`: ターゲットアカウントに作成されるロール
`stacksets-exec-xxxxxxxxxxxx`: ターゲットアカウントに作成されるロール

# Service Catalog
CloudFormationテンプレートをアップロードしてカタログ管理できる
開発者側に権限を与えずに、Service　CatalogにあるCloudformationのテンプレートを起動出来たりする
（デフォルトでは複数アカウントには共有不可だがCloudFromationのStackSetsを使用できる）

## ポートフォリオ
ポートフォリオの単位でユーザーに製品の使用を許可することができる
ポートフォリオを他のAWSアカウントに共有することが可能
ポートフォリオを作成してそこに製品を追加するイメージ

## 製品
CloudFormationテンプレートをパッケージ化したもの
バージョン管理可能

## 制約
### テンプレート制約
CloudformationのテンプレートのEC2インスタンスタイプやリージョンとかパラメータで指定したものに対してt2だけなど制約をつけたりできる

### 起動制約
リソースをプロビジョニングするのに利用するロール
ユーザーの権限を最小にしたまま、製品の起動が可能

### 通知制約
スタックのイベントを受け取ることができる

# デプロイ戦略
## ローリングアップグレード
既存の環境に新しいバージョンのコードを導入し、古いコードを廃止しながら徐々に新しい環境を増やしていく

## カナリアデプロイ
新しいバージョンを古いバージョンと一緒にデプロイして、新しいバージョンを使用するユーザーを慎重に制御する。検証を監視し、調整しつつ、徐々に人口を増やしていく
ローリングとは異なり検証を挟んで他のところにもリリースしていいか確かめる

## ブルーグリーンデプロイ
現在の環境（ブルー）に影響を与えず、分割された新しい環境に新しいバージョン（グリーン）をデプロイし、ロードバランサーの接続先をグリーンに切り替える
OKだったらブルーを削除

## 参考
https://syobochim.hatenablog.com/entry/2020/03/18/144432#%E3%83%AD%E3%83%BC%E3%83%AA%E3%83%B3%E3%82%B0%E3%82%A2%E3%83%83%E3%83%97%E3%82%B0%E3%83%AC%E3%83%BC%E3%83%89-Rolling-Upgrade