# 3rd partyの決済システムを使用するのにおいて使用するIPの数の制限がある場合
NATゲートウェイをとおしてEC2インスタンスへの支払いリクエストがサードパーティソフトウェアにルーティングされる設定をすることです。こうすることで、NATゲートウェイのIPアドレスとサードパーティソフトウェアとを連携させて、直接にEC2インスタンスをソフトウェアに接続しない構成を実現することができます。NATゲートウェイにホワイトリストに登録されたElastic IPアドレスをアタッチして、それにサードパーティーのクレジットカード支払いシステムを連携させれば、NATゲートウェイの後ろでEC2インスタンスは5台以上利用することが可能になる。

## スポットインスタンス＋SQSによる処理の遅延の発生
スポットインスタンスに可視性タイムアウトを組み合わせて処理を進めている。スポットインスタンスは長い時間の処理を任せている場合に、途中で停止してしまう可能性がある臨時的に利用されるべきインスタンス。スポットインスタンスは確かにコストは最も安いため費用対効果が高いが、それによって長時間処理のタスクがストップしてしまうことが問題。スポットインスタンスが途中で停止されてしまった場合、可視性タイムアウトを設定しているため、その処理は完全にストップしてしまう。
スポットインスタンスをオンデマンドインスタンスへと変更することで、こうした待機時間を解消することが可能