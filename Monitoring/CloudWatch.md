# CloudWatch
## Metrix
- CloudWatchはAWSのあらゆるサービスのメトリクスを提供
- メトリクスは = 監視する変数（CPUUtilization、NetworkIn...）
- メトリクスは名前空間に属する
- ディメンジョン = メトリック（インスタンスID、環境など）の属性
- メトリックあたり最大10個のディメンション
- メトリクスにはタイムスタンプがある
- メトリクスのCloudWatchダッシュボードを作成できる

## Custom Metrix
- 独自のカスタムメトリクスを定義してCloudWatchに送信することが可能
- ディメンション（属性）を使用してメトリクスを細分化
    - instance.id
    - Environment.id
- メトリック分解能（StorageResolution APIパラメータ - 2つの選択可能な値）
    - Standard：1分（60秒
    - High Resolution：1秒 - 高コスト
- PutMetricDataAPIコールを使用
- スロットルエラー時にexpバックオフを使用する

## EC2 Detailed Monitoring
- EC2インスタンスのメトリクスは5分ごと
- Detailed monitoring（有料）では、1分ごと
- ASGをより迅速にスケールさせたいならDetailed monitoring推奨
- AWS Free Tierでは10種類の詳細なモニタリングメトリクスを提供
- 注意: EC2 のメモリ使用量はデフォルトではプッシュされない (インスタンスの内部からカスタムメトリックとしてプッシュする設定が必要)

## CloudWatch Dashboard
- ダッシュボードを設定して、キーとなるメトリクスに素早くアクセスすることができます。
- グローバルサービス
- ダッシュボードには、複数リージョンのグラフ表示が可能
- ダッシュボードのタイムゾーンと時間帯は変更可能
 - 自動更新（10秒、1m、2m、5m、15m）の設定
- 価格
    - 3つのダッシュボード（最大50のメトリクス）が無料
    - 3ドル/ダッシュボード/月 
## AWS CloudWatch Logs
- アプリケーションはSDKを使用してCloudWatchにログを送信することができます。
- 以下のサービスからログ取得可能
    - Elastic Beanstalk: アプリケーションからのログ収集
    - ECS：コンテナからの回収
    - AWS Lambda：関数ログからのコレクション
    - VPC Flow Logs:VPC固有のログ
    - API Gateway
    - フィルタに基づくCloudTrail
    - CloudWatchのログエージェント：例えばEC2マシン上で
    - Route53: DNSクエリのログ
- CloudWatchログの送信先
    - アーカイブのためのS3へのバッチエクスポート
    - さらなる分析のためのElasticSearchクラスタへのストリーム

- Logs storage architecture:
    - ロググループ：任意の名前、通常はアプリケーションを表す
    - ログストリーム：アプリケーション内のインスタンス／ログファイル／コンテナ
- ログの有効期限ポリシーを定義することができる（無期限、30日など）
- AWS CLIを使ったCloudWatchのログ尾行
- CloudWatchにログを送信するには、IAMのパーミッションが正しいことを確認
- セキュリティ：グループレベルでKMSを使用したログの暗号化

## CloudWatch Logs Metric Filter & Insights
- CloudWatchログではフィルタ式を使用することができます。
    - ex)ログの中から特定の IP を発見
    - Metric filtersによってアラームをトリガー
- CloudWatch Logs Insights（2018年11月~）
    - ログをクエリして、CloudWatch Dashboardsにクエリを追加するために使用することができます。

## Logs for EC2
- デフォルトでは、EC2マシンからのログはCloudWatchに送信されない
- EC2上でCloudWatchエージェントを実行して、必要なログファイルをプッシュする
- IAMのパーミッションが正しいことを確認
- CloudWatchのログエージェントはオンプレミスでも設定可能

## CloudWatch Logs Agent & Unified Agent
- 仮想サーバー（EC2やオンプレミスサーバーなど）用
- CloudWatchログエージェント
    - 旧バージョンのエージェント
    - CloudWatchログへの送信のみ可能
- CloudWatch ユニファイドエージェント
    - RAM、プロセスなどの追加のシステムレベルのメトリクスを収集
    - サーバのログを収集し、CloudWatch Logsに送信
    - SSMパラメータストアを使用した一元化された設定
    - メトリクス
        - CPU (active, guest, idle, system, user, steal)
        - Disk metrics (free, used, total), Disk IO (writes, reads, bytes, iops)
        - RAM (free, inactive, used, total, cached)
        - Netstat (number of TCP and UDP connections, net packets, bytes) • Processes (total, dead, bloqued, idle, running, sleep)
        - Swap Space (free, used, used %)

## CloudWatch Alarms