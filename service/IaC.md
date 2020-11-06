# Opsworks
ChefやPuppetなどのサーバー設定ツールを元にした構成管理のサービス  
アプリケーションのデプロイ・スケーリング・保守の自動化が可能  
主にEC2上のミドルウェアやアプリケーションの設定管理を担い、デプロイ、モニタリング、スケーリングを行う。
CloudformationでOpsWorksを構築することも可能


## CloudFormation + Opsworks
CloudFormation内でOpsWorksコンポーネント（スタック・レイヤー・インスタンス・アプリケーション）をモデリングし、それらをCloudformation スタックとしてプロビジョニングすることができる

# CloudFormation
## DeletionPolicy
スタックが削除されてもリソースを保持する設定。

## Resource Tags プロパティ
Resource Tags プロパティを使用してリソースにタグを適用し、それらのリソースの識別や分類に役立てることができます。タグを適用できるのは、AWS CloudFormation がタグ付けをサポートしているリソースのみです。

## CreationPolicy 属性
CreationPolicy 属性をリソースに関連付けて、AWS CloudFormation が指定数の成功シグナルを受信するかまたはタイムアウト期間が超過するまでは、ステータスが作成完了にならないようにします。リソースにシグナルを送信するには、cfn-signal ヘルパースクリプトまたは SignalResource API を使用できる。
### ResourceSignalパラメーター
timeoutを指定することでタイムアウト時間を設定することができる。
