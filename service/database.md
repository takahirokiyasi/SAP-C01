# DynamoDB
リザーブドキャパシティがあり、割安な価格で運用することができる。

## グローバルテーブル
マルチリージョンにマルチマスターデータベースをデプロイする機能

## オンデマンドキャパシティ設定
テレビのリアルタイム投票など利用者が急激に増えるスパイクが起こるようなサービスなどで、スケーリングの設定をauto scalingではなくオンデマンドキャパシティ設定にすることによって、スパイクに対応できる。
料金はリクエスト数に対しての課金

# RedShift
DWH(データウェアハウス)
標準 SQL および既存のビジネスインテリジェンス (BI) ツールを使用して、すべてのデータをシンプルかつコスト効率よく分析できる。
クエリ最適化、高パフォーマンスストレージでの列指向ストレージ、および並列クエリ実行を使用して、ペタバイト単位の構造化データに対して複雑な分析クエリを実行可能

## クロスリージョンスナップショット
スナップショットはS3に保存されるが、直接操作することはできない。別リージョンにスナップショットを転送するクロスリージョンスナップショット機能を使って可用性を高める。