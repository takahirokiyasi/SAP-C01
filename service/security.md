# AWS System Manager
## AWS System Manager パラメータストア(SSM)
構成データ管理のための安全で階層的なストレージを提供し秘密の管理を行います。パスワード、データベース文字列、マシンイメージ(AMI)ID、およびパラメータ値としてのライセンスコードなどのデータを保管できます。  
プレーンテキストまたは暗号化データの形式で保管でき、参照できます。  
Systems Manager パラメータをスクリプト、コマンド、 SSM ドキュメント、構成、および自動化ワークフローを、 パラメーターの作成時に指定します。

### パラメータストアとSecret managerの違い
設定とシークレットにひとつのストアが欲しい場合、パラメータストア
ライフサイクル管理を備えたシークレット専用のストアが欲しい場合、Secret Manager
パラメータストアは無料、Secret Managerは有料

# NACL（ネットワークACL）
ステートレス
サブネットに対して設定

# セキュリティグループ
ステートフル

# IAM
## タグでの運用制限
IAMグループとIAMポリシーを使って
特定のタグが付与されたインスタンスのみ操作できるなどの運用を実現することができる。

## 外部IDの信頼条件設定
サードパーティのサービスにリソースのアクセス許可をする場合はIAMロールについているIAMポリシーに外部IDを信頼条件として設定し、外部ID＋ロールのARNでAssumeRoleすることで安全に外部アクセスさせることができる。

## リソースベースのIAMポリシーの付与によるクロスアカウントアクセス
ロールによってアクセス許可が付与される場合は元から与えられていたアクセス許可を放棄しないといけないが（元のアクセス許可とは？？）
リソースベースのポリシーで許可した場合は元のアクセス許可は保持されるというメリットがある

# AWS Resource Access Manager(AWS RAM)
所有する特定のAWSリソースを他のAWSアカウントと共有できます。AWS OrganizationsでTrusted Accessを有効にするにはAWS RAM CLIから、enable-sharing-with-aws-organizationsコマンドを使用します。

# ACM
リージョン単位で証明書を発行する

# AWS CloudHSM
AWS上に利用者専用のハードウェアがプロビジョニングされ、サーバーを含めて暗号化キーを利用者が管理することができる。
ハードウェアセキュリティモジュール (HSM) では、不正使用防止策の施されたハードウェアデバイス内での、安全なキー保管と暗号化操作が可能になる。HSM は暗号キーデータを安全に保存し、ハードウェアの暗号境界の外側からは見えないようにキーデータを使用できるよう設計されている。

# AWS Shield
## AWS Shield Standard
インフラストラクチャ (レイヤー 3 および 4) を標的とするすべての既知の攻撃を総合的に保護できる。
自動的に適用されていて追加料金なし
## AWS Shield Advanced
レイヤー7の攻撃も防ぐことができる（DDos）
Amazon CloudWatch でのほぼリアルタイムの通知と、AWS WAFとAWS Shieldマネジメントコンソールあるいは API での詳細な診断によって、DDoS 攻撃に対する完全な可視性を与えてくれる。
AWS WAF and AWS Shieldマネジメントコンソールから以前の攻撃の要約を表示することもできる。

# CloudHSM
クラウドベースのハードウェアセキュリティモジュール (HSM) 
クラウドで暗号化キーを簡単に生成して使用できるようになります。
CloudHSM で、FIPS 140-2のレベル3認証済みのHSMを使用して、暗号化キーを管理できる。

# AWS Organizations
## SCP
制御できるのは各リソースに対しての権限の最大範囲（S3に対する読み取り・書き込み・管理全て）だけで、別にIAMグループ＋IAMロールとかでS3読み取りのみの制限を加える必要がある。

# Directory Service
## Simple AD
比較的小規模かつ的コストのマネージド型ユーザーディレクトリが必要な場合に利用できる
AWS上に独立したドメインを作成し、Samba4プロトコルに対応したActive Directory互換のディレクトリとして利用できる。
## AD Connector
オンプレミス環境やAWS上にある既存のディレクトリサービスへの認証プロキシのような役割を果たす。
ユーザー情報を保持するわけではない。
AD Connectorを経由することで既存のディレクトリサービスを利用して認証処理を行うことが可能

## AWS Directory Service for Microsoft Active Directory(AWS Managed Microsoft AD)
オンプレミスのADと信頼関係を構築できる。
[AWS Managed Microsoft ADのユースケース](https://docs.aws.amazon.com/ja_jp/directoryservice/latest/admin-guide/ms_ad_use_cases.html)



