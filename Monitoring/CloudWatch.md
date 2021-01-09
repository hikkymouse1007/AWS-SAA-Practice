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