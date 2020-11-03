# SMS(Server Migration Service)
VMware vSphereインフラストラクチャからAmazon EC2に仮想マシンをインポートすることができる。  
AWS Server Migration Serviceは、数千のオンプレミスワークロードを従来よりも簡単に、かつ短時間でAWSに移行できるサービスです。AWS SMS では、ライブサーバーボリュームの増分レプリケーションの自動化、スケジュール設定、および追跡が可能なため、大規模なサーバーの移行作業を簡単に実施することができる。

## AWS Connector for vCenter
AWS SMSを利用する際は、AWS Connector for vCenterと呼ばれるコネクターを移行対象となるオンプレミス環境のサーバーにインストールします。これによって、GUIを使用してVMware vSphereインフラストラクチャからAmazon EC2に仮想マシンをインポートできます。VMware vCenter から Amazon EC2に移行する仮想マシンから EC2 インスタンスを起動します。AWS Connector for vCenter を使用して仮想マシンを Amazon EC2 に移行する。

# AWS License Manager
ソフトウェアベンダーのライセンスを簡単に管理できるようになるサービス。
AWS License Manager により、管理者はライセンス契約の規約をエミュレートするカスタマイズされたライセンスルールを作成し、EC2 のインスタンスの起動時にそれらのルールを適用できる。
管理者はこれらのルールを使用して、契約が定める以上のライセンスを使用する、または短期的に異なるサーバーにライセンスを再割り当てするといったライセンス違反を規制できる。
AWS License ManagerはAWSのサービスと統合し、1つのAWSアカウントを通じて、複数のアカウント、ITカタログ、オンプレミスにわたってライセンスの管理を簡素化する。
ライセンス管理者はAWS Service Catalogでルールを追加できる。

# VM Import/Export
仮想マシン(VM)イメージを既存の仮想化環境からAmazon EC2にインポートし、それを元の環境にエクスポートすることができます。この方法を使うと、アプリケーションおよびワークロードを Amazon EC2 へ移行したり、VM イメージカタログを Amazon EC2 にコピーしたり、バックアップと災害対策のために VM イメージのリポジトリを作成することができます。
