# Opsworks
ChefやPuppetなどのサーバー設定ツールを元にした構成管理のサービス  
アプリケーションのデプロイ・スケーリング・保守の自動化が可能  
主にEC2上のミドルウェアやアプリケーションの設定管理を担い、デプロイ、モニタリング、スケーリングを行う。
CloudformationでOpsWorksを構築することも可能


## CloudFromation + Opsworks
CloudFormation内でOpsWorksコンポーネント（スタック・レイヤー・インスタンス・アプリケーション）をモデリングし、それらをCloudformation スタックとしてプロビジョニングすることができる
