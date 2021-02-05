## Q1 VPC Endpoint
### VPCエンドポイント
https://dev.classmethod.jp/articles/vpc-endpoint-gateway-type/

- Interface Endpoint
プライベートSubからENI経由でエンドポイントに接続して、VPC外部のリソースへアクセス
- Gateway Endpoint
プライベートからSエンドポイントのグローバルIPへ接続し、外部リソースへアクセスS3とDynamoDBのみに適用される

## Q2  Transit Gateway
https://business.ntt-east.co.jp/content/cloudsolution/column-102.html
ネットワークのハブとして、大量のVPCやオンプレミス環境の接続を簡単にしてくれる

>Amazon VPCの数が数百から数千と多くなり、それらを相互に接続していく必要がある場合、ピアリングのための設定や管理運用は煩雑になる恐れがあります。複数のAmazon VPCと複数のオンプレミス拠点とをつなぐ必要が場合にはその煩雑さは一層増すでしょう。

このような大規模な組織のニーズに応え、数百、数千の接続を簡単に構築できるのが、「AWS Transit Gateway」というサービスです。AWS Transit Gatewayを、数多あるAmazon VPCやオンプレミス環境などが接続する、ネットワークの中央に位置したハブのように見立てて管理できます。

Amazon VPC間の相互のピアリングの必要はなく、それぞれをAWS Transit Gatewayに接続するだけで、同じく接続されたほかのAmazon VPCやオンプレミス環境にも接続可能となります。接続先のAmazon VPCやAWS VPN、AWS Direct Connectゲートウェイ、別のAWS Transit Gatewayと柔軟に接続してスケーリングできるのです。

## Q5 CloudTrail
https://docs.aws.amazon.com/ja_jp/awscloudtrail/latest/userguide/how-cloudtrail-works.html
**デフォルトでは Amazon S3 のサーバー側の暗号化 (SSE) を使用して、CloudTrail イベントログファイルが暗号化されます**。ログファイルを AWS Key Management Service (AWS KMS) キーで暗号化する選択もできます。 バケットにログファイルを任意の期間、保存することができます。また、Amazon S3 ライフサイクルのルールを定義して、自動的にログファイルをアーカイブまたは削除することもできます。ログファイルの配信と確認に関する通知が必要な場合は、Amazon SNS 通知を設定できます