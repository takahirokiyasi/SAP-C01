## RTO（目標復旧時間 recovery time objective）
RTOは、インシデントが発生してから通常業務をオンラインに戻すまで「ダウンを許容できる最大時間」を指す。
## RPO(目標復旧時点 recovery point objective)
RPOとは、インシデント後に通常運営を再開するために「復旧しなければならないバックアップファイルの古さ」を示す。
例）
2時にサーバーが停止してしまった場合にRPOが15分だと1時45分までのデータは復旧しないといけない
1時45分から2時までの間のデータは失われる。
## 障害復旧モデル
リージョン間だけでなくオンプレ＋AWSクラウド上の話でも同じ
### バックアップ&リストア
バックアップファイルのみを別のリージョンに退避しておく考えかた
### パイロットライト
停止した状態のサーバーを別のリージョンに用意しておき、障害発生時に立ち上げる
### ホットスタンバイ
同じ構成のサーバーやネットワーク機器をスケールダウンした状態で置いておき、障害発生時に切り替える
### マルチサイト
アクティブーアクティブな構成でルーティングで均等なトラフィックをそれぞれに流す