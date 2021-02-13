# Rekognition
AWS CLI または Rekognition APIを使用
画像から人物特定とかテキスト抽出とか人の装備とかをチェックするのが得意

## Rekognition DetectFaces
検出された各顔を含むJSON応答を返すことができる。  
Amazon Rekognitionは画像検品ソリューションに特化したサービスではない（代わりにG2インスタンスに
ソフトウェアを導入する方が良い）
※画像をバイトコードに変換してデータを受け渡す方法はサポート外

## Amazon Rekognition Video
Amazon Rekognition Video ではライブビデオストリーミングをリアルタイムで解析して、顔を検出し、判別できます。Amazon Kinesis Video Streams のストリーミングデータを Rekognition Video に入力し、最大数千万もの顔データと照らし合わせて、超低レイテンシーでの顔認識を行います。バッチ処理のユースケースとして、Amazon Rekognition Video では Amazon S3 に保存した録画データを解析することもできる。
Amazon Rekognition Videoは、ストリーミングビデオの分析を開始および管理するために使用できるストリームプロセッサ（CreateStreamProcessor）を提供します。

# Polly
テキストの音声読み上げサービス