# High Availability and Scalability
## 垂直スケーラビリティ
- インスタンスのサイズの拡張
ex: t2.micro => t2.large
- Databaseなどnon-distributedなシステムでは一般的
ex: RDS, ElastiCacheなど

## 水平スケーラビリティ
- インスタンスの数を増やす
- distributedシステムで適用される
- EC2など

##　高スケーラビリティ
- 水平スケーリングによって連携する
- 最低2つ以上のAZに配置する
- 目標はデータセンターの障害時にインスタンスが生きること

=> EC2においては
- Vertical Scaling => インスタンスサイズの拡大・縮小
- Horizontal Scaling => インスタンス数の増減
    - AutoScalingGroup
    - Load Balancer
- High Availability: マルチAZ
    - AutoScalingGroup multi AZ
    - Load Balancer multi AZ

## ELBの用途
- アクセス不可の分散
- 一つのアクセスポイントのみ公開する(DNS)
- シームレスにEC2のエラーをハンドリング
- EC2のヘルスチェック可能
- SSLの目的地(HTTPS)を提供
- クッキーの接着を強制(ELBのスティッキーセッション)
https://docs.aws.amazon.com/ja_jp/elasticloadbalancing/latest/classic/elb-sticky-sessions.html
https://blog.serverworks.co.jp/tech/2017/01/05/elb-sticky/
- 複数ゾーンに跨る高アベイラビリティー(可用性)
- プライベート通信とパブリック通信の分離

## Health Check
- ロードバランサーに接続さているインスタンスがリクエストに応答可能かをロードバランサーに伝える
-  ヘルスチェックはポートとルート(/health)で行われる
- 5秒おきにインスタンスのステータスを確認
- レスポンスが200でなければunhealty

## Types of load balancer
- CLB(HTTP/HTTPS/TCP)
- ALB(HTTP/HTTPS/WebSocket)
- NLB(TCP/TLS/UDP)
- CLBは非推奨

## ELBとSG
- ApplicationSGとLoadBalancerSG
- LBとEC2の間の通信において、EC2にELBからのリクエストしか受け付けないSGを設ける事ができる。
=> この設定を間違えるとunhealtyになる

## Tips
- LBは瞬時のスケーリングはできない.
暖気申請が必要。
- トラブルシューティング
    - 4xx: クライアントエラー
    - 5xx: App側のエラー
    - 503: LBの稼働限界またはターゲット登録なし
    - LBがアプリに接続できない場合、SGを見直す。
- モニタリング
    - ELBは全てのアクセスログを記録する。
    CloudWatchメトリクスで集計データ(aggregate statistics)の確認ができる。