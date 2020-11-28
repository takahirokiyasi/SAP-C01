# Opsworks
ChefやPuppetなどのサーバー設定ツールを元にしたサーバー管理のサービス  
アプリケーションのデプロイ・スケーリング・保守の自動化が可能  
主にEC2上のミドルウェアやアプリケーションの設定管理を担い、デプロイ、モニタリング、スケーリングを行う。
CloudformationでOpsWorksを構築することも可能

## CloudFormation + Opsworks
CloudFormation内でOpsWorksコンポーネント（スタック・レイヤー・インスタンス・アプリケーション）をモデリングし、それらをCloudformation スタックとしてプロビジョニングすることができる

## 自動ヒーリング
インスタンスが Amazon EC2 ヘルスチェックに合格した場合でも、スタック内にある異常なインスタンスまたは失敗したインスタンスを再起動します。スタックのレイヤー設定では、自動ヒーリングがデフォルトで有効化されています。自動ヒーリングが有効になっている場合、ポータルのダウンタイムを回避するために、失敗したEC2インスタンスは自動的に置き換えられます。

## Opsworksエージェント
発行された一連のコマンドを取得
レシピを実行する

## Opsworksスタックのライフサイクルイベント
- Setup
Appサーバーの起動の終了後に発火
- Configure
- Deploy
- Undeploy
- shutdown

## AWS Opsworks for Chef Automate
完全マネージド型のChefサーバを数クリックで作成し、ワークフローの自動化を設定することができます。
また、インスタンス設定ではサブネットやセキュリティグループ、バックアップの時間などを指定することができるようになっているほか、Chefコミュニティで配布されているCookbookなどと互換性があるため、サンプルコードとして利用することができます。
Chef Automateと言うサービスがAWS上で使用できるので今までChef Automate使ってる場合は便利

# CodeCommit
## 他のレポジトリサービスからの移行
レポジトリをクローンし、CodeCommitにPushする

# Code Star
開発環境のスキャホールドと言えます。リポジトリ（CodeCommit）からビルド（CodeBuild）、そしてデプロイ（CodeDeploy）がCodePipelineでシームレスに繋がります。さらにデプロイするEC2インスタンスやBeanstlak環境も作成します。 開発者は、 最小限の情報を入力することで、AWS上で動くアプリケーション環境と最小限のソースコードを手に入れることができる。
VScodeなどの開発ツール連携してて、CI・CDの環境とつながることもできる。
料金は発生しない。作成したリソースに対して費用発生
CI/CD環境を初めてつくる時に便利

# Code Artifact
ソフトウェアパッケージを安全に保存、公開、共有できるようにするプライベートリポジトリサービス

# CloudFormation
## DeletionPolicy
スタックが削除されてもリソースを保持する設定。

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

# デプロイ戦略
## ローリングアップグレード
既存の環境に新しいバージョンのコードを導入し、古いコードを廃止しながら徐々に新しい環境を増やしていく

## ブルーグリーンデプロイ
現在の環境に影響を与えず、分割された新しい環境に新しいバージョンをデプロイ

## カナリアデプロイ
新しいバージョンを古いバージョンと一緒にデプロイして、新しいバージョンを使用するユーザーを慎重に制御する。検証を監視し、調整しつつ、徐々に人口を増やしていく

## 参考
https://syobochim.hatenablog.com/entry/2020/03/18/144432#%E3%83%AD%E3%83%BC%E3%83%AA%E3%83%B3%E3%82%B0%E3%82%A2%E3%83%83%E3%83%97%E3%82%B0%E3%83%AC%E3%83%BC%E3%83%89-Rolling-Upgrade