# Amazon SQS 
https://dev.classmethod.jp/articles/relay_looking_back_sqs/#toc-amazon-simple-queue-servicesqs
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

## SQS - Security
- 暗号化:
    - HTTPS APIを使用した機内暗号化
    - KMSの鍵を使用したアットレスト暗号化
    - クライアント自身で暗号化/復号化を行いたい場合は、クライアント側の暗号化
- アクセスコントロール:IAMポリシーによるSQS API へのアクセス制限 
- SQS アクセスポリシー（S3バケットポリシー風, JSON)
    - SQSキューへのクロスアカウントアクセスに便利
    - 他のサービス(SNS、S3...)がSQSキューに書き込むことを許可するのに便利

## Visibility Timeout
>可視性タイムアウトとは、 SQS のキューに入ったメッセージが処理開始直後に重複して処理されないように、処理中の場合、一時的に他のプロセスからは、メッセージが存在していることを見えないようにする設定です。
https://dev.classmethod.jp/articles/sqs-visibility-timeout/

## Dead Letter Queue
コンシューマが可視性タイムアウトの時間以内にメッセージの処理に失敗した場合、メッセージはキューに戻る
- メッセージがキューに戻る回数(MaximumReceives)のしきい値を設定できる
- MaximumReceives のしきい値を超えた後、メッセージはデッドレターキュー（DLQ）に入る
- アプリのキューを使った処理のデバッグに使用できる
- DLQは保有期間があるので期限切れの前にメッセージを処理すること(推奨保有期間設定:14日間)