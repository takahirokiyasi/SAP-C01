# EMR
Amazon EMR の中心的なコンポーネントはクラスターです。クラスターとは、Amazon Elastic Compute Cloud (Amazon EC2) インスタンスのコレクションのことです。クラスター内の各インスタンスは、ノードと呼ばれます。各ノードには、クラスター内での役割があり、ノードタイプと呼ばれます。Amazon EMR は、各ノードタイプにさまざまなソフトウェアコンポーネントもインストールし、Apache Hadoop などの分散型アプリケーションでの役割を各ノードに付与します。

EMRでSQLクエリを使用した分析は実施できない
現在Redhatサーバーで実行されているプロセスを再現することはできないため、ECインスタンスにRedhatサーバーをインストールして解析ソフトウェア自体を移行する方が適切

## ノードタイプ
### マスターノード
処理を行うために他のノード間でのデータおよびタスクの分散を調整するソフトウェアコンポーネントを実行することで、クラスターを管理するノードです。マスターノードは、タスクのステータスを追跡し、クラスターの状態を監視します。すべてのクラスターにはマスターノードがあり、マスターノードのみで 1 つのノードクラスターを作成することができます。
### コアノード 
タスクを実行し、クラスター上の Hadoop Distributed File System (HDFS) にデータを保存するソフトウェアコンポーネントを持つノードです。マルチノードクラスターには、少なくとも 1 つのコアノードがあります。
### タスクノード
 タスクを実行するのみで、HDFS にデータを保存しないソフトウェアコンポーネントを持つノードです。タスクノードはオプションです。

### インスタンスの割り当て
中長期的に使用する場合はマスターノードやコアノードにはリザーブドインスタンスを利用して、ジョブに割り当てられるタスクノードにはスポットインスタンスを使用することが望ましい

# Glue
データソースからのデータの抽出
データの取り込みや分析をしやすいデータ形式への変換
データ分析基盤に対するデータの取り込み
と言うETLタスク(バッチ処理)をマネージドで提供するサービス

## Amazon S3 データレイクに対するクエリ
AWS Glueクローラーを利用してS3バケットを処理して、データをスキャンして、分類、スキーマ情報を抽出し、
メタデータをAWS Glueデータカタログに保存してデータ分析前のETL処理を行うことができます。

AWS Glue では、AWS に保存されたデータを指定するだけで AWS Glue によるデータ検索が行われ、テーブル定義やスキーマなどの関連するメタデータが AWS Glue データカタログに保存されます。AWS Glue データカタログ は、データの場所、スキーマ、およびランタイムメトリクスへのインデックスです。Data Catalog 内の情報を使用して、ETL ジョブを作成し、監視します。Data Catalog の情報はメタデータテーブルとして保存され、各テーブルが 1 つのデータストアを指定します。さらにAWS Glueでのデータ処理後に、Data Catalogに保持したデータに対してRedShift・EMR・Athenaなどで即座に解析処理を実行することが可能


# Kinesis
## Kinesis Data Streams
レコードの順序付け、および複数のAmazon Kinesisアプリケーションに対して同じ順序でレコードを読み取ったり再生したりする機能を提供
デフォルトで２４時間レコードを保持する
SQSとAmazon Kinesis Data Streamsを一緒に利用する必要はない
データの種類や用途に応じてストリームを作成する。ストリームは１つ以上のシャードによって構成される。
データは「データレコード」と呼ばれる
データレコードにはシーケンス番号がふられ、順番が保証される
タスクの実行にはLambdaなどが必要

### Kinesis エージェント
データ送信側(プロデューサー)
スタンドアロンの Java ソフトウェアアプリケーションで、データを収集して Kinesis Data Streams に送信する簡単な方法を提供する。

### Kinesis Producer Library(KPL)
データ送信側(プロデューサー)
Kinesis Data Streamsにデータを送信するOSSのライブラリ

Aggregation：複数件のデータを 1 データレコードに集約して送信可能
Collection：複数のレコードをバッファリングして送信

### Fluent plugin for Amazon Kinesis
データ送信側(プロデューサー)
Fluentd をログ収集に使っているなら、このプラグインを追加するだけで AmazonKinesis へのデータ投入がすぐにできる

### Amazon Kinesis Data Generator (KDG)
データ送信側(プロデューサー)
Amazon Kinesis Streams または Amazon Kinesis Firehose に`テストデータ`を簡単に送信できる

### Amazon Kinesis Client Library(KCL)
コンシューマー (データ処理側)
Java、Ruby、Python、Node.js の開発に利用できる OSSのクライアントライブラリ

1. Record Processor Factory 
  レコードプロセッサーを作る
2. Record Processor
Amazon Kinesis Streamsのシャードから取り出したデータを処理するプロセッサーの単位
3. Worker
個々のアプリケーションインスタンスとマッピングする処理単位
KCL WorkerはStreamと対応してログを受け取るプログラムのこと

### シャードが不十分な場合の対処法
#### リシャーディング
シャードの分割を行いデータ容量を増やす
シャードの分割では１ つのシャードを 2 つシャードに分けます。シャードの結合では、2 つシャードを 1 つのシャードに組み合わせます。リシャーディングは、1 回のオペレーションでシャードに分割できる数と 1 回のオペレーションで結合できるシャードの数が 2 個以下に限られるという意味で、常にペアワイズです。リシャーディングオペレーションの対象となるシャードまたはシャードペアは、親シャードと呼ばれます。リシャーディングオペレーションを実行した結果のシャードまたはシャードペアは、子シャードと呼ばれます。 分割によりストリーム内のシャードの数が増え、したがってストリームのデータ容量は増えます。シャード単位で請求されるため、分割によりストリームのコストが増えます。

#### シャードに合わせたインスタンスサイズの増強
Kinesisストリーム内のインスタンスのサイズとシャードの数を増やすことで、インスタンスがインスタンス内で並行して実行されるより多くのレコードプロセッサを処理できるようにします。 また、ストリームが送信されるデータのレートに適切に対応できるようにします。ストリームのデータ容量は、ストリームに指定するシャードの数の関数です。

## Amazon Kinesis Video Streams
動画はこちらを使う
### HTTP Live Streaming (HLS)機能
Kinesis Video Streams の HTTP Live Streaming (HLS) 機能を使用して、ライブ動画や録画したメディアを Kinesis Video Streams からお使いのブラウザまたはモバイルアプリケーションに簡単にストリーミングできます。
HLSをサポートするサードパーティプレーヤーを使用してKinesis Video Streamsと統合できる
#### GetHLSStreamingSessionURL API
GetHLSStreamingSessionURL APIによりHLSストリーミングセッションURLを取得することができます。
HLSストリーミングセッションURLを取得したら、ビデオを再生できるビデオプレーヤーに提供します。 

Amazon Kinesis ビデオストリームは以下の３つの形式を利用できます。
### GetMedia:
GetMedia API を使用して、Kinesis ビデオ ストリームを処理する独自のアプリケーションを構築します。GetMedia は低レイテンシーのリアルタイム API です。GetMedia を使用するプレーヤーを作成する場合は 、自分で構築する必要があります。 

### HLS
HTTP Live Streaming (HLS) は、業界標準の HTTP ベースのメディアストリーミング通信プロトコルです。HLS を使用して、ライブ再生またはアーカイブ済み動画の再生用に Amazon Kinesis ビデオストリーム を表示できます。

### MPEG-DASH
Dynamic Adaptive Streaming over HTTP (DASH) (MPEG-DASH とも呼ばれる) は、従来の HTTP ウェブサーバーから配信されたインターネット経由で高品質のストリーミングを可能にする適応ビットレートストリーミングプロトコルです。 



## Kinesis Client Library(KCL)
Kinesisアプリケーションを構築し、ストリーミングデータを使用してリアルタイムダッシュボードの強化、アラートの生成、動的な価格設定と広告の実装などを行うことができます。 Kinesis Data StreamsからAmazon Simple Storage Service（Amazon S3）、Amazon Redshift、Amazon EMR、AWS Lambdaなどの他のAWSサービスにデータを送信することが可能です。今回は目的であれば、大量のストリームデータをS3に蓄積して、 Redshiftによる解析を実施することができます。

## Kinesis Data Firehose
ストリーミングデータをデータレイクやデータストア、分析ツールに確実にロードする
ストリーミングデータをキャプチャして変換し、Amazon S3、Amazon Redshift、Amazon Elasticsearch Service、Splunk にロードする
配信ストリームを作成してデータを配信
Lambdaを利用したデータの変形が可能

## Amazon Kinesis S3コネクター
Amazon Kinesis から Amazon S3 にデータをアーカイブすることができます。Amazon Kinesis コネクタライブラリを使用すると Amazon Kinesis を他の AWS サービスやサードパーティー製ツールと簡単に統合できるようになります。
Amazon Kinesis コネクタライブラリを使用するには、Amazon Kinesis クライアントライブラリ (KCL) が必要です。このライブラリの現在のバージョンでは、Amazon DynamoDB、Amazon Redshift、Amazon S3、Amazon Elasticsearch Service に対するコネクタが提供されています。

## Kinesis Analytics
datastreamやfirehoseからデータを受け取る
ストリームデータを標準的なSQLクエリでリアルタイムに分析　分析結果を再度DataStreamsやFirehoseに流すこと可能
SQLクエリ実行前に、Lambdaによる前処理可能
