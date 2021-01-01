# Amazon SQS 
## Amazon SQS – Standard Queue
- アプリケーションの分離(decoupling)に使用されるフルマネージドサービス
- 特徴
    - 無制限のスループット、キュー内のメッセージ数無制限
    - メッセージの保持期間:デフォルトで4日間、最大14日間 
    - 低レイテンシ(発行および受信時に 10 ms 未満)
    - 1回のメッセージ送信で256KBの制限
    
- 重複したメッセージを持つことがある
- 順不同のメッセージを持つことがある


## Amazon SQS – Producing Messages
- SDK（SendMessage API）を使用してSQSに生成する。
- メッセージは、コンシューマが削除するまで SQS に保持される
- 例：処理順を送信する場合
    - 注文ID
    - 顧客ID
    - 任意の属性
- SQS Standard：無制限のスループット

## SQS – Consuming Messages
- 動作するコンシューマ（EC2インスタンス、サーバー、AWS Lambda)
- SQSにメッセージをポーリング（一度に10個までのメッセージを受信する
- メッセージ処理（例：RDS データベースにメッセージを挿入する） 
- DeleteMessage API によるメッセージ削除