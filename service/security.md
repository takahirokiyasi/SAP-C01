
# セキュリティグループとネットワーク ACL (NACL) の比較
次の表は、セキュリティグループとネットワーク ACL の基本的な違いをまとめたものです。

| セキュリティグループ | ネットワーク ACL |
|-|-|
|インスタンスレベルで動作します。| サブネットレベルで動作します。|
|ルールの許可のみがサポートされます|ルールの許可と拒否がサポートされます|
|ステートフル: ルールに関係なく、返されたトラフィックが自動的に許可されます | ステートレス: 返されたトラフィックがルールによって明示的に許可されます|
|トラフィックを許可するかどうかを決める前に、すべてのルールを評価します|トラフィックを許可するかどうかを決定する際に、最も低い番号のルールから順にルールを処理します。|
|インスタンスの起動時に誰かがセキュリティグループを指定した場合、または後でセキュリティグループをインスタンスに関連付けた場合にのみ、インスタンスに適用されます。|関連付けられているサブネット内のすべてのインスタンスに自動的に適用されます（そのため、セキュリティグループのルールの許容範囲が広すぎる場合は、保護レイヤーを追加する必要があります）。|


# IAM
## タグでの運用制限
IAMグループとIAMポリシーを使って
特定のタグが付与されたインスタンスのみ操作できるなどの運用を実現することができる。

## 外部IDの信頼条件設定
サードパーティのサービスにリソースのアクセス許可をする場合はIAMロールについているIAMポリシーに外部IDを信頼条件として設定し、外部ID＋ロールのARNでAssumeRoleすることで安全に外部アクセスさせることができる。

## リソースベースのIAMポリシーの付与によるクロスアカウントアクセス(S3のバケットポリシーとかの話)
ロールによってアクセス許可が付与される場合は元から与えられていたアクセス許可を放棄しないといけないが（クロスアカウントアクセスの場合ロールの切り替えをしないといけない）
リソースベースのポリシーで許可した場合は元のアクセス許可は保持されるというメリットがある

## IAMロールによるクロスアカウントアクセス（Switch Role）
IAMユーザーからクロスアカウントアクセス用IAMロールに切替ができる
Switch Roleを活用することにより各アカウントのIAMユーザーとしてログインしなおすことなしにセキュアに管理コンソールを切り替え可能

まずはスイッチ先でロールを作成し、スイッチ元でロールの切り替え機能を設定する。
例えば開発アカウントを使って、本番環境のS3データを更新するようなケースで利用できたりする。
機構としてはSTSのAssumeRoleを使用している。
[switchロールの実装方法](https://dev.classmethod.jp/articles/iam-switchrole-for-beginner/)

### Jumpアカウントを作成してクロスアカウントアクセス
開発環境・本番環境など環境ごとにアカウントを分けた上でJumpアカウントを作成し、Jumpアカウントでswitchロールを使って各アカウントに対してアクセス権限を持つ構成にする。

[マルチアカウントのベストプラクティス](https://d0.awsstatic.com/events/jp/2017/summit/slide/D4T2-2.pdf)

## IAM ポリシー
### 信頼ポリシー
IAMロールを引き受けると信頼するプリンシパルを定義するJSONポリシードキュメント。ロール信頼ポリシーは、IAMのロールにアタッチされる必須のリソースベースのポリシーです。信頼ポリシーで指定できるプリンシパルには、ユーザー、ロール、アカウント、およびサービスが含まれます。 

## IAM Permission Boundary
誰でも柔軟に各アカウントで権限のポリシーの付け替えができるようにしたいが、必要以上に権限を付けられたくない場合に使う
### ユースケース
1. 組織管理者が絶対禁止したいものをSCPで定義
1. 各アカウントのIAM管理者が Permission Boundary で利用者の権限範囲を定義
1. 利用者は Permission Boundary で定義された範囲のポリシーのみ利用できる

[IAM Permission Boundaryについて](https://qiita.com/14kw/items/46a0054d90a4f5d779c4#iam-permissions-boundary)

# STS(Security Token Service)
IAMの一機能  
一時的セキュリティ認証情報を持つ、信頼されたユーザーを作成および提供することができる。
ユーザーにはロールが紐づいている

[STSによる一時的な認証情報の付与について](https://dev.classmethod.jp/articles/cm-advent-calendar-2015-getting-started-again-aws-iam/#toc-16)
## ID フェデレーションでのSTSの使用
### ウェブIDフェデレーション
基本的にはAWS Cognitoを使うことで実現できるが、直接AssumeRoleWithWebIdentity APIを呼び出しても実現できる（Cognitoを使う時もAssumeRoleWithWebIdentity APIを叩いている Cognito推奨）
Google・Amazon・Facebook認証とかで使用する。

アクセスキーID等が返される

### エンタープライズIDフェデレーション
組織のネットワークのユーザーを認証し、ユーザーが AWS にアクセス可能にすることができる。
Microsoft Active Directory を活用できる。

#### カスタムフェデレーションブローカー
※社内でSAML 2.0をサポートしていない社内のID認証システムを使う場合（LDAP（Lightweight Directory Access Protocol）による認証など）
組織の認証システムを使用して AWS リソースへのアクセスを許可することができる。

1. AssumeRole
ユーザーを認証するためのカスタムIDブローカーを作成し。AssumeRoleを用いてAWSリソースへのアクセスに使用できる一時的なセキュリティ認証情報のセットを取得し、AWSリソースにアクセス。
これらの一時的な資格情報は、アクセスキーID、シークレットアクセスキー、およびセキュリティトークンで構成される。
通常、AssumeRoleはアカウント内またはクロスアカウントアクセスに使用します

端的に言うと、AssumeRoleはRoleArnを入力するとCredentialsを返すAPI

2. GetFederationToken
GetFederationTokenではフェデレーションユーザーの一連の一時的なセキュリティ資格情報（アクセスキーID、シークレットアクセスキー、およびセキュリティトークンで構成される）を返す。
Federatedユーザは、事前準備なし(`IAMロール作成不要`)で利用したい時にだけ一時的な権限を発行することができるため、アクセスキーがあれば権限発行できる。
AssumeRoleと違ってデフォルトの有効期限が大幅に長い（デフォルト12時間で90m~36hを指定可能）


既存の IAM ユーザーに一時的セキュリティ認証情報のセットが返ります。この API は、MFA が IAM ユーザーに対して有効なときに AWS リクエストを作成するなど、セキュリティを強化するために役立つ

##### 一時クレデンシャルを利用したAWS Management Consoleログイン用URL作成の流れ
AssumeRoleもGetFederationTokenでもほぼ同じ
1. アクセスキー、シークレットアクセスキー、トークンの3セットを入手 
2. 取得した情報からセッション文字列を生成 
3. セッション文字列をURLエンコード 
4. AWSフェデレーションエンドポイントにリクエストを送信してサインイントークンを取得 
5. ログイン用URLを作成

[手順](https://dev.classmethod.jp/articles/aws-management-console-login-without-iam-password/)

#### SAML2.0を用いたフェデレーション
組織の認証システムとSAMLを使用して、AWSリソースへのアクセスを許可することができる。

AssumeRoleWithSAML

##### IAM ID プロバイダー
外部 ID プロバイダー (IdP) サービスとの連携を設定する場合、IAM IDプロバイダーを作成し、IdPとその設定について AWS に通知します。これにより、AWS アカウントと IdP の間の「信頼」が確立される。

コンソール・CLI・APIで作成可能

### GetSessionToken
`AWSアカウント`または`IAMユーザー`の一時的な認証情報のセットを返します。資格情報は、アクセスキーID、シークレットアクセスキー、およびセキュリティトークンで構成。(AssumeRoleは`IAMロール`に対して)

- `MFA`を使用してAmazon EC2 StopInstancesのような特定のAWS APIオペレーションへのプログラム呼び出しを保護する場合
- モバイルデバイスやウェブブラウザのような安全性の低い環境から IAM ユーザーで AWS リソースにアクセスする場合

# AWS Resource Access Manager(AWS RAM)
所有する特定のAWSリソースを他のAWSアカウントと共有できます。AWS OrganizationsでTrusted Accessを有効にするにはAWS RAM CLIから、enable-sharing-with-aws-organizationsコマンドを使用します。

# ACM
リージョン単位で証明書を発行する
ACM以外にもSSL証明書をIAMに保存することもできるが非推奨

# AWS CloudHSM
AWS上に利用者専用のハードウェアがプロビジョニングされ、サーバーを含めて暗号化キーを利用者が管理することができる。
ハードウェアセキュリティモジュール (HSM) では、不正使用防止策の施されたハードウェアデバイス内での、安全なキー保管と暗号化操作が可能になる。HSM は暗号キーデータを安全に保存し、ハードウェアの暗号境界の外側からは見えないようにキーデータを使用できるよう設計されている。

## SSL/TLS オフロード(SSL アクセラレーション)
オフロードにより、ウェブサーバーの`プライベートキー`を HSM に保存することで、ウェブサーバーの計算の負担が軽減され、セキュリティが強化されます。

[仕組み](https://docs.aws.amazon.com/ja_jp/cloudhsm/latest/userguide/ssl-offload-overview.html)

# AWS Shield
## AWS Shield Standard
インフラストラクチャ (レイヤー 3 および 4) を標的とするすべての既知の攻撃を総合的に保護できる。
自動的に適用されていて追加料金なし
SYNフラッドやUDPリフレクション攻撃などの多くの一般的なDDoS攻撃を防ぐ
`CloudFront`と`Route53`が対象

## AWS Shield Advanced
レイヤー7の攻撃も防ぐことができる（DDos）
Amazon CloudWatch でのほぼリアルタイムの通知と、AWS WAFとAWS Shieldマネジメントコンソールあるいは API での詳細な診断によって、DDoS 攻撃に対する完全な可視性を与えてくれる。
AWS WAF and AWS Shieldマネジメントコンソールから以前の攻撃の要約を表示することもできる。

CloudFrontとRoute53に追加で
`ELBとEIP`とGlabalAcceleratorに対しても設定できる。

## AWS WAF
`CloudFront`と`APIgatewayのREST API`と`ALB`と`App Sync GraphQL`に対応
## Web ACL(WEBアクセスコントロールリスト)
Webアクセス制御リスト(Web ACL)は、保護されたリソースが応答するWebリクエストをルールを適用して許可したり・ブロックできる。
以下がよく利用されるルール
- リクエストの IP アドレスの送信元
- リクエストの送信元の国
- リクエストの一部に含まれる文字列一致または正規表現（regex）一致
- リクエストの特定の部分のサイズ
- 悪意のある SQL コードまたはスクリプトの検出

指定したルール以外のときどうするかのデフォルトアクションをどうするか

### Web ACL キャパシティーユニット
Web ACLに必要なリソースを定義・運用する

## AWS Firewall Manager
複数アカウントでのWAFやShieldの管理が簡単にできるやつ

# CloudHSM
クラウドベースのハードウェアセキュリティモジュール (HSM) 
クラウドで暗号化キーを簡単に生成して使用できるようになります。
CloudHSM で、FIPS 140-2のレベル3認証済みのHSMを使用して、暗号化キーを管理できる。

AWS CloudHSMを使用して、WebサーバーのSSL / TLS処理をオフロードできます。この処理にCloudHSMを使用すると、Webサーバーの負担が軽減され、Webサーバーの秘密キーをCloudHSMに保存することでセキュリティが強化されます。 SSLおよびTLSは、WebサーバーのIDを確認し、インターネット上で安全なHTTPS接続を確立するために使用される。

# CloudTrail
## S3バケット削除したユーザーの確認
S3バケット削除のリクエストはAWSアカウントから行われ、リソースはメインAWSアカウントの一部であるため、CloudTrailによってアカウントのログ記録を確認し、使用されているユーザートークンを照合することでユーザーを確認することができます。
また特定のアクティビティの確認のためには、メインAWSアカウントへの役割を引き受ける時点で、AWSアカウントがsharedEventIdおよびuserIdentity情報を持つエントリを作成している必要があります。
これを使用して、DeleteBucket APIを実行する必要がある実際のAWSユーザーを特定できます。

### sharedEventId
異なる AWS アカウントに送信される同じ AWS アクションから CloudTrail イベントを一意に識別するために CloudTrail によって生成された GUID。
### userIdentity
リクエストを行った IAM ID のタイプとどの認証情報が使用されたかに関する詳細が含まれる
userのArnやAccountIdなどが含まれている

## Cloud Watch Logsによるユーザーアクティビティの追跡
CloudWatch LogsイベントによってCloudTrail API呼び出しでトレースできます。これにより、ユーザーの不適切なリソース利用を監視し、問題を分析することで、余分なリソース利用に即時対応ができる仕組みを整備できる。

## CloudTrail Insight
ログの異常なアクティビティを検出する
（SNSと連携して異常検知したらメールを送信とかは今のところできない）

## マルチアカウントでログの集約
集約先のアカウントにS3のバケットを作成して、バケットポリシーで別アカウントのCloudTrailからのPutObjectを許可する。

# AWS Cognito
基本的に提供してるサービスのお客様向け
（当たり前だけどAWS SSOでAmazon Cognito ユーザープールをアイデンティティソースとしては利用出来ない　SSOは従業員に対してMicrosoft ADとか使ってシングルサインオンでAWSにアクセスできる機能）

## ユーザープール
`アプリへのアクセスに利用できるトークン（JWT）を発行・提供`
認証・認可を制御するためのメイン機能
Cognitoユーザーの管理・サインインサインアップ機能・外部IDプロバイダーやcognito発行のトークン管理などを行う。

Amazon Cognito のユーザディレクトリ
ユーザープールを使用すると、ユーザーは Amazon Cognito を通じてウェブまたはモバイルアプリにログインできる。(入力されたユーザーのメールアドレスやパスワードを検証・認証できる)
また、ユーザーは Google、Facebook、Amazon、Apple などのソーシャル ID プロバイダー、および SAML ベースの ID プロバイダー経由でユーザープールにサインインすることもできる。

リージョン毎にユーザープールを作成
APIGateway・Appsync・ALB(Cognito認証 実用性はない)で連携可能で認証できる

### グループ
IDプールと連携可能でユーザープールのグループでIDプールで与えるロールとか設定できたりする

### アプリクライアント
HostedUI・API呼び出し・OAuthなどでユーザープールにアクセスできる

## フェデレーティッドアイデンティティ(IDプール)
`AWSにアクセスできるクレデンシャルを提供`
ユーザープールのアカウントに対して、IAMロールの付与を行う機能（AWSへの一時的なクレデンシャルを発行）
S3へのアクセスをクライアントから直接できたりできる

## Cognito Sync
IDプールで管理されるユーザー単位に、デバイス間でアプリケーションデータを同期する機能
新規で開発する場合、AWS AppSyncというがあるので現在非推奨

# AWS SSO
従業員に対してMicrosoft ADとか使ってシングルサインオンでAWSにアクセスできるサービス
STSを使ってSSOを実現するのではなく、より簡単にSSOを実現する。導入に必要なタスクがより少なくなった

AWS SSO内にADをデフォルトで作れる（外部のADにも接続できる。オンプレのADだったり、AWS Managed Microsoft ADだったり）

SAMLやOIDCを⽤いたIDフェデレーションをサポート
外部のアイデンティティに、IAMロールのセッションを⽤いてAWSリソースへのアクセスを可能としている

これ見ればOrganizationとの統合やSAML連携がよくわかる（複雑なので丸投げ）
↓
[AWSにおけるシングルサインオンの設計と仕組み](https://d1.awsstatic.com/webinars/jp/pdf/services/20200722_AWSBlackbelt_%E3%82%B7%E3%83%B3%E3%82%B0%E3%83%AB%E3%82%B5%E3%82%A4%E3%83%B3%E3%82%AA%E3%83%B3%E3%81%AE%E8%A8%AD%E8%A8%88%E3%81%A8%E9%81%8B%E7%94%A8.pdf)

## Organizationsとの統合
- AWS Organizations マスターアカウントから、ユーザー毎のアクセス権限を⼀元管理
既存の社内認証情報を使用できる

## SAMLのサポート
SAMLが利用できるアプリケーションへのSSOアクセスをすることができる

# Directory Service
## Simple AD
比較的小規模かつ的コストのマネージド型ユーザーディレクトリが必要な場合に利用できる
`AWS上に独立したドメイン`を作成し、Samba4プロトコルに対応したActive Directory互換のディレクトリとして利用できる。

## AWS Directory Service for Microsoft Active Directory(AWS Managed Microsoft AD)
既存のADインフラストラクチャを使用してAD対応ワークロードをAWSクラウドに移行する場合は、AWS Managed Microsoft AD が適当。
AD 対応アプリケーションと AWS アプリケーションにユーザーはオンプレミスAD 認証情報でアクセスでき、そのためのユーザー、グループ、またはパスワードの同期が不要。
オンプレミスのADと信頼関係を構築できる。
たとえば、ユーザーは既存の AD ユーザー名とパスワードを使用して、AWS マネジメントコンソールとAmazon WorkSpaces にサインインできます。また、SharePoint などの AD 対応アプリケーションに認証情報の再入力なしでアクセスできるようになる。

[AWS Managed Microsoft AD　＋　SSO](https://dev.classmethod.jp/articles/enable-aws-single-sign-on-howto/)

## AD Connector
オンプレミス環境やAWS上にある既存のディレクトリサービスへの認証プロキシのような役割を果たす。
ユーザー情報を保持するわけではない。
AD Connectorを経由することで既存のディレクトリサービスを利用して認証処理を行うことが可能

前提条件
- VPCがオンプレとVPNかDirectConnectで繋がっている必要がある。
- ２つのサブネットが必要
https://dev.classmethod.jp/articles/try-active-directory-connector/

## オンプレミスのADにSSOを接続する
AD ConnectorかAWS Managed Microsoft ADが使用可能
### AWS Managed Microsoft ADの場合
双方向の信頼関係を作成する
AWS Managed Microsoft AD とオンプレミスの Active Directory との間で作成される`双方向`の信頼関係により、オンプレミスのユーザーは会社の認証情報を使用してさまざまな AWS サービスおよびビジネスアプリケーションにサインインできる。
一方向の信頼では AWS SSO では機能しない
https://docs.aws.amazon.com/ja_jp/singlesignon/latest/userguide/connectonpremad.html

# SSO
1度のログインにより複数のサービスにアクセスすることができる機能  
[AWS 公式　シングルサインオンの運用](https://d1.awsstatic.com/webinars/jp/pdf/services/20200722_AWSBlackbelt_%E3%82%B7%E3%83%B3%E3%82%B0%E3%83%AB%E3%82%B5%E3%82%A4%E3%83%B3%E3%82%AA%E3%83%B3%E3%81%AE%E8%A8%AD%E8%A8%88%E3%81%A8%E9%81%8B%E7%94%A8.pdf)
AWSマネジメントコンソールへのフェデレーションアクセスではなく、AWSへのAPIアクセスのフェデレーションを実装する場合は、SSOエンドポイントではなくSTSのAssumeRoleWithSAML

## IDフェデレーション
SSOを実現するための一つの手段、それぞれのID管理基盤はそのままに、ネットワークドメインをまたいで互いに“連携”させるもの（今までは社内などの単一ネットワークドメインのだけだった）

## プロトコル
OAuth・OpenID Connect・SAMLはSSOを実現するためのプロトコル

OAuthは認可（権限を与える）のプロトコル
OpenID ConnectやSAMLは認証（身元を明らかにする）のプロトコル

OpenID ConnectはOAuthの拡張規格のためSNSのOAuth認証と同時に利用され
SAMLはActive Directory Federation Services (ADFS) などに利用されている。

# KMS
https://dev.classmethod.jp/articles/10minutes-kms/

# Amazon Macie
`S3内`のデータを機械学習を使用してデータを自動的に特定および分類することで、`機密データ`を検出して保護するのに役立つフルマネージド型のサービス
住所や名前、クレジットカード情報を検出できる
Organizationでのマルチアカウントをサポート
