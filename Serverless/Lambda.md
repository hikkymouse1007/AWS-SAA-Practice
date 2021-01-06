# Lambda
## Pricing
- コール数に応じて課金される
- 最初の1,000,000リクエストは無料
- 以降、100万リクエストごとに0.20ドル（1リクエストあたり0.0000002ドル
- 持続時間ごとに課金• Easy Pricing:
• Pay per request and compute time
• Free tier of 1,000,000 AWS Lambda requests and 400,000 GBs of compute time
• Integrated with the whole AWS suite of services
• Integrated with many programming languages
• Easy monitoring through AWS CloudWatch
• Easy to get more resources per functions (up to 3GB of RAM!) • Increasing RAM will also improve CPU and network!• Easy Pricing:
• Pay per request and compute time
• Free tier of 1,000,000 AWS Lambda requests and 400,000 GBs of compute time
• Integrated with the whole AWS suite of services
• Integrated with many programming languages
• Easy monitoring through AWS CloudWatch
• Easy to get more resources per functions (up to 3GB of RAM!) • Increasing RAM will also improve CPU and network!(100ms単位で)
- 無料の場合は月あたりの計算時間が40万GB-seconds - 関数が1GB RAMの場合は40万秒
- 関数が128MB RAMの場合、3,200,000秒
- その後60万GB秒で1ドル
- とにかく安価

## Lambda Integration
- API Gateway
- Kinesis
- DynamoDB
- S3
- CloudFront
- CloudWatch Events EventBridge
- CloudWatch Logs
- SNS
- SQS
- Cognito

## Lambda Limits per region
- Execution
    - メモリ割り当て 128MB～3008MB（64MB単位
    - 最大実行時間：900秒（15分
    - 環境変数 (4 KB)
    - 関数コンテナ」のディスク容量 (/tmp内)：512 MB - 並行処理の実行回数：1000回 (増加可能) 1000回 (増加可能)

- Deployment
    - ラムダ関数の配置サイズ（zip）：50 MB
    - 圧縮されていないデプロイメントのサイズ（コードと依存関係）：250 MB
    - 起動時に/tmpディレクトリを使用して他のファイルを読み込み可能
    - 環境変数のサイズ 4 KB