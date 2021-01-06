# SNS
- 一つのメッセージを複数の送信先に送りたい場合
=> pub/sub(publish/subscribe)パターン
- イベント提供者はSNSにメッセージを一つのSNSトピックに送るだけで良い
- イベント受信者は任意の数だけ設定可能
- トピックの各サブスクライバは全てのメッセージを受信する(note:フィルター機能のアップデート)
- 1トピックあたり最大1,000,000,000件の購読者数
- 10万トピック制限
- サブスクライバは、以下のいずれか
    - SQS
    - HTTP / HTTPS (with delivery retries – how many times)
    - Lambda
    - Emails
    - SMS messages
    - Mobile Notifications

- SNSへでデータ送信は以下のAWSサービスが使用可能
    - CloudWatch (for alarms)
    - Auto Scaling Groups notifications
    - Amazon S3 (on bucket events)
    - CloudFormation (upon state changes => failed to build, etc) • Etc...

## SNSの公開方法
- トピックの発行 (SDKの利用) 
    - トピックの作成
    - １つまたは多数のサブスクリプションを作成する
    - トピックに公開する

- ダイレクトパブリッシュ（モバイルアプリ用SDK)
    - プラットフォームアプリケーションの作成
    - プラットフォームのエンドポイントを作成する
    - プラットフォームのエンドポイントに公開
    - Google GCM、Apple APNS、Amazon ADMなどとの連携

## Security
- 暗号化
    - HTTPS APIを使用した機内暗号化
    - KMSの鍵を使用したアットレスト暗号化
    - クライアント自身で暗号化/復号化を行いたい場合のクライアント側の暗号化

- アクセスコントロール:SNS APIへのアクセスを規制するIAMポリシー
- SNSのアクセスポリシー（S3のバケットポリシーlike)
- SNSのトピックへのクロスアカウントアクセスに便利
- 他のサービス(S3...)がSNSのトピックに書き込めるようにするのに便利

## SNS + SQS: Fan Out
- 一つのSNSへのプッシュ=>サブスクライバの複数のSQSが受信
- 責務の完全分離データ損失なし
- SQSは次を許可：データの永続性、遅延処理、作業の再試行 
- サブスクライバを後からいつでも追加する機能
- SQSキューのアクセスポリシーからSNSに書き込みを許可する
- SNSはSQS FIFOキューにメッセージを送信できない（AWSの制限)

## Application: S3 Events to multiple queues
- イベントタイプ（例：オブジェクトの作成）とプレフィックス(例: images/)に対しては、S3のイベントルールは1つしか設定できない。
- 同じS3イベントを多くのSQSキューに送りたい場合は、fan-outを使用する
=> S3からSNSへ送信、SNSからSQSへfan-out