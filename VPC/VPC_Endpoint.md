# VPC Endpoint
- エンドポイントはパブリックなWWWネットワークではなく、プライベートネットワークを使ってAWSサービスに接続することが可能
- 水平方向にスケールし、冗長性あり
- AWSサービスにアクセスするためのIGW、NATなどの必要性を排除
- インターフェース: ENI(プライベートIPアドレス)をエントリーポイントとして提供(セキュリティグループを設定する必要がある) - ほとんどのAWSサービスが対象
=> "Enable DNS name"の項目を設定する
- ゲートウェイ: ターゲットをプロビジョニングし、ルートテーブルで使用する必要がある - S3とDynamoDB
- 問題が発生した場合
- VPCのDNS設定の解決方法を確認する - CheckRouteTables

エンドポイントを設定したルートテーブルにはGatewayのリソースに紐づくエンドポイントが送信先に追加される
![スクリーンショット 2021-01-20 22 47 07](https://user-images.githubusercontent.com/54907440/105183488-9a1dac00-5b71-11eb-84da-dd77525ec159.png)

## CLIの注意点
CLIのデフォルトのリージョンがus-east-1なので、
引数で自分のリージョンを渡す必要がある
(ここではap-northeast-1)
[ec2-user@ip-10-0-21-146 ~]$ aws s3 ls

[ec2-user@ip-10-0-21-146 ~]$ aws s3 ls --region ap-northeast-1
2020-11-24 14:23:20 cf-templates-kuibvgd8silh-ap-northeast-1
2020-12-31 23:33:17 hiroki-mycloudfront-content
2021-01-20 12:59:17 hiroki-vpc-flowlogs
2020-12-01 13:06:28 mashimo-cloudformation-stack
