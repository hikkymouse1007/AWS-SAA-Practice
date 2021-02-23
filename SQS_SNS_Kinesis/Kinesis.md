## Kinesis
- Apache Kafkaの代替
- アプリケーションログ、メトリクス、IoT、クリックストリームに最適
- リアルタイムなビッグデータに最適
- ストリーミング処理フレームワーク（Spark、NiFiなど）に最適 
- データは自動的に3つのAZにレプリケート
- 3つのKinesis
    - Kinesis Streams: 低レイテンシの大規模なストリーミング集計
    - Kinesis Analytics: SQLを使用してストリームのリアルタイム分析を実行 
    - Kinesis Firehose: S3、Redshift、ElasticSearchにストリームをロード

## Kinesis Stream
- ストリームは順番にシャード(1つのキューのようなもの)/パーティションに分割
- データ保持はデフォルトで1日、最大7日まで可能
- データの再処理/再生能力
- 複数アプリケーションが同じストリームを消費可能
- スループットのスケールに合わせたリアルタイム処理
- キネシスに一度挿入したデータは削除できない(不変性)

## Kinesis Stream Shard
- １つのstreamは複数のシャード(断片)からなる
- １シャードあたりの書き込み時1MB/秒または1000メッセージ/秒の処理性能
- 1シャードあたりの読み込み時:2MB/sの処理性能
- 課金は、プロビジョニングされたシャードごとに行われ、必要な数だけシャードを持つことができる
- シャードの数は、時間の経過とともに変化する(reshard / merge)
- レコードはシャードごとに発注

## Kinesis Security
- IAMポリシーを使用したアクセス/認可の制御
- HTTPSエンドポイントを使用した飛行中の暗号化
- KMSを使用した安静時の暗号化
- クライアント側でデータを暗号化/復号化可能(設定難)
 - KinesisがVPC内でアクセスできるVPCエンドポイント

 ## Kinesis CLI
 GUIからあらかじめStreamを作成しておく
 ```
 aws kinesis list-streams                                       
{
    "StreamNames": [
        "myFirstSteam"
    ]
}

 aws kinesis describe-stream --stream-name myFirstSteam                                       

{
    "StreamDescription": {
        "Shards": [
            {
                "ShardId": "shardId-000000000000",
                "HashKeyRange": {
                    "StartingHashKey": "0",
                    "EndingHashKey": "340282366920938463463374607431768211455"
                },
                "SequenceNumberRange": {
                    "StartingSequenceNumber": "49614242006608270452876510010732281151460677135512895490"
                }
            }
        ],
        "StreamARN": "arn:aws:kinesis:ap-northeast-1:364357082259:stream/myFirstSteam",
        "StreamName": "myFirstSteam",
        "StreamStatus": "ACTIVE",
        "RetentionPeriodHours": 24,
        "EnhancedMonitoring": [
            {
                "ShardLevelMetrics": [
                    "IncomingBytes",
                    "OutgoingRecords",
                    "IteratorAgeMilliseconds",
                    "IncomingRecords",
                    "ReadProvisionedThroughputExceeded",
                    "WriteProvisionedThroughputExceeded",
                    "OutgoingBytes"
                ]
            }
        ],
        "EncryptionType": "NONE",
        "KeyId": null,
        "StreamCreationTimestamp": 1609768796.0
    }
}

aws kinesis put-record --stream-name myFirstSteam --data "user signup" --partition-key user_123
{
    "ShardId": "shardId-000000000000",
    "SequenceNumber": "49614242006608270452876510010853173733422193860333797378"
}

aws kinesis put-record \
          --stream-name myFirstSteam \
          --data "user login" \
          --partition-key user_123
{
    "ShardId": "shardId-000000000000",
    "SequenceNumber": "49614242006608270452876510010854382659241844704672743426"
}

aws kinesis put-record \
          --stream-name myFirstSteam \
          --data "user visits home page" \
          --partition-key user_123
{
    "ShardId": "shardId-000000000000",
    "SequenceNumber": "49614242006608270452876510010855591585061461532870705154"
}

aws kinesis get-shard-iterator --stream-name myFirstSteam --shard-id shardId-000000000000 --shard-iterator-type TRIM_HORIZON
{
    "ShardIterator": "AAAAAAAAAAEBjJteXHT/mcXfSqHtUSBzgCYGpb97vnSe0yu2Ttk009FcWTSJ9TwGoXKM43uLq74/URy2TIkP1A9NVag9KAVKyrIdpyQqLd8d78Vg7/ydRHhde3sTZjO4vGN8TrFAj3KuBqO5hup0PAQsrtoB5M9dsmC+OFGU9xNKhLFmOigmxMrCOUmTUWMeCija0S29GoXyHuFTAqDUhxMShauNkWwI"
}

aws kinesis get-records --shard-iterator "AAAAAAAAAAEBjJteXHT/mcXfSqHtUSBzgCYGpb97vnSe0yu2Ttk009FcWTSJ9TwGoXKM43uLq74/URy2TIkP1A9NVag9KAVKyrIdpyQqLd8d78Vg7/ydRHhde3sTZjO4vGN8TrFAj3KuBqO5hup0PAQsrtoB5M9dsmC+OFGU9xNKhLFmOigmxMrCOUmTUWMeCija0S29GoXyHuFTAqDUhxMShauNkWwI"
{
    "Records": [
        {
            "SequenceNumber": "49614242006608270452876510010853173733422193860333797378",
            "ApproximateArrivalTimestamp": 1609769576.78,
            "Data": "dXNlciBzaWdudXA=",
            "PartitionKey": "user_123"
        },
        {
            "SequenceNumber": "49614242006608270452876510010854382659241844704672743426",
            "ApproximateArrivalTimestamp": 1609770104.567,
            "Data": "dXNlciBsb2dpbg==",
            "PartitionKey": "user_123"
        },
        {
            "SequenceNumber": "49614242006608270452876510010855591585061461532870705154",
            "ApproximateArrivalTimestamp": 1609770136.818,
            "Data": "dXNlciB2aXNpdHMgaG9tZSBwYWdl",
            "PartitionKey": "user_123"
        }
    ],
    "NextShardIterator": "AAAAAAAAAAF6bHMbZHD8JY7kCHb12Kub834QIYs8QCn8CnAt38iR3opAhtlm0qMwqUhVBsdC6y6XuiOkCBJ3fj0n4OlV1VA0u6DUz1vwk9h7ud1AbtR7a+rMfmILhDhVnUpyHQWVWvNy1+85pwPFOgjQ2UPrl6X799cz5TD3tE2CDbGS7ej8siHyQUhd4XT+2YRuddPu+1ymVf/9hm2M8JVkoaZ7PBT7",
    "MillisBehindLatest": 0
}
 ```
取得したDataは以下でデコード可能
https://www.base64decode.org/

## Kinesis Data Firehose
- フルマネージドサービス、管理不要、自動スケーリング、サーバーレス
- Redshift / Amazon S3 / ElasticSearch / Splunkにデータを読み込む
- ほぼリアルタイム処理
    - 非完全バッチ処理の場合は最低60秒のレイテンシまたは一度に最低32MBのデータ送信
- 多くのデータフォーマット、変換、変換、圧縮をサポート

## Data Stream vs Firehose
- Data Stream
    - カスタムコード作成(producer/consumer)
    - リアルタイム（～200ms)
    - スケーリング（シャードの分割/マージ）管理が必要
    - 1～7日分のデータストレージ、再生機能、マルチコンシューマー
- Firehose
    - 完全管理、S3、Splunk、Redshift、ElasticSearchへの送信
    - Lambdaを使ったサーバーレスのデータ変換
    - リアルタイムに近い（バッファの最小時間は1分
    - 自動スケーリング
    - データストレージなし

## Kinesis Data Analytics
- SQLを使用したKinesis Streamsによるリアルタイム分析
- 自動スケーリング
- マネージド：プロビジョニングするサーバーが不要
- 持続性：リアルタイム
- 実質の消費率に対しての課金
- リアルタイムクエリからストリームを作成することができる