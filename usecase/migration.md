# RDS と　RDB on EC２の移行時の使い分け
基本的にはRDSを使うが以下の場合はRDB on EC2を使う。
- RDSが対応していないRDBやバージョンを使いたい場合
- 最大CPU数やメモリサイズが不足する場合
- メンテナンス時間を完全にコントロールしたい場合
- OS（カーネル）に何か特殊な設定が必要な場合

https://www.slideshare.net/AmazonWebServicesJapan/rdbmsaws-75043784?ref=https://dev.classmethod.jp/