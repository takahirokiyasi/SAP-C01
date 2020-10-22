# LDAP（Lightweight Directory Access Protocol）による認証
カスタムIDブローカーによって、LDAPによるユーザー認証を実施することで、ユーザーはAWSリソースにアクセスすることができる。

# Storage gatewayにおけるDDoS攻撃や不正アクセスからの防御
※AWS Storage Gatewayのボリュームゲートウェイを使用し、iSCSIを介したハイブリッド構成のデータ共有システムを利用している場合

iSCSI接続を実施するためにCHAP（Challenge Handshake Authentication Protocol）を構成し、iSCSIとイニシエーター接続を認証することで、ボリュームと VTL（仮想テープライブラリ）デバイスターゲットへのアクセスの認証時に、iSCSI イニシエータのアイデンティティを定期的に確認してプレイバック攻撃から保護することができます。

AWS Storage Gateway はCHAPを使用して、ゲートウェイと iSCSI イニシエータの間の認証を行うことができます。 CHAP は、ボリュームと VTL デバイスターゲットへのアクセスの認証時に、iSCSI イニシエータのアイデンティティを定期的に確認することにより、プレイバック攻撃から保護します。 CHAP を設定するには、AWS Storage Gateway コンソールと、ターゲットへの接続に使用される iSCSI イニシエータソフトウェアの両方で、設定を行う必要があります。Storage Gateway では相互 CHAP が使用され、イニシエータがターゲットを認証するときに、ターゲットもイニシエータを認証します。

## その他の付属情報
### iSCSI
SCSIコマンド、データの転送をIPに変換して通信する方式で、イーサネットのインフラが使用できるため、安価にストレージネットワークを構築でき、FC-SANのような専門知識が必要な管理者が不要
SCSIのネットワークを使った版
### SCSI
コンピュータと周辺機器を接続するための規格のひとつ
SCSIではホスト側をイニシエータ、デバイス側をターゲットと呼ぶ。
### CHAP
CHAP（Challenge Handshake Authentication Protocol）とは、PPPなどで使用されるユーザ認証の一方式