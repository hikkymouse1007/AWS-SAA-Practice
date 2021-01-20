## Flow Logs
- インターフェイスに入るIPトラフィックの情報をキャプチャ
    - VPC フローログ
    - サブネットフローログ
    - ENIフローログ
- 接続性の問題の監視とトラブルシューティングに役立つ
- フローログのデータはS3 / CloudWatch Logsに送信可能
- AWSが管理するインターフェースからもネットワーク情報をキャプチャ(ELB, RDS, ElastiCache, Redshift, WorkSpaces) 

## Flow Log Syntax
- 基本形式

```
<version> <account-id> <interface-id> <srcaddr> <dstaddr> <srcport> <dstport> <protocol> <packets> <bytes> <start> <end> <action> <log- status>

```

- Srcaddr, dstaddr:問題のあるIPを特定
- Srcport, dstport:問題のあるポートを特定
- Action : セキュリティグループ/NACLによるリクエストの成否
- 利用パターンや悪意のある行動の分析に使用可能
- フローログ例：https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html#flow-log-records

VPCフローログの設定
![スクリーンショット 2021-01-20 23 25 48](https://user-images.githubusercontent.com/54907440/105188135-eae3d380-5b76-11eb-8fc9-69c4f6d7dad5.png)

S3へ送信されたログ
![スクリーンショット 2021-01-20 23 25 21](https://user-images.githubusercontent.com/54907440/105188130-e91a1000-5b76-11eb-8090-ce5da1d90514.png)

CloudWatchへ送信されたログ
![スクリーンショット 2021-01-20 23 26 40](https://user-images.githubusercontent.com/54907440/105188192-f9ca8600-5b76-11eb-9efc-57786f287460.png)

Athenaによる解析

- テーブルの作成
```
CREATE EXTERNAL TABLE IF NOT EXISTS vpc_flow_logs (
         version int,
         account string,
         interfaceid string,
         sourceaddress string,
         destinationaddress string,
         sourceport int,
         destinationport int,
         protocol int,
         numpackets int,
         numbytes bigint,
         starttime int,
         endtime int,
         action string,
         logstatus string 
) PARTITIONED BY (
         `date` date 
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' LOCATION 's3://hiroki-vpc-flowlogs/AWSLogs/364357082259/vpcflowlogs/ap-northeast-1/' TBLPROPERTIES ("skip.header.line.count"="1");
```

- パーティションの追加
```
ALTER TABLE vpc_flow_logs
ADD PARTITION (`date`='2021-01-20')
location 's3://hiroki-vpc-flowlogs/AWSLogs/364357082259/vpcflowlogs/ap-northeast-1/2021/01/20';
```

- RejectIPの解析クエリ実行

```
SELECT day_of_week(date) AS
  day,
  date,
  interfaceid,
  sourceaddress,
  action,
  protocol
FROM vpc_flow_logs
WHERE action = 'REJECT' AND protocol = 6
LIMIT 100;
```