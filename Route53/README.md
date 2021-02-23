# Route53
## 基礎知識
- マネージドDNS(Domain Name System)
- 代表的なレコード
    - A: hostname to IPV4
    - AAAA: hostname to IPV6
    - CNAME: hostname to hostname
    - Alias: hostname to AWS resource

- Route53で使用可能なもの
    - パブリックドメイン
    - VPC内で名前解決されたプライベートドメイン

- 追加の特徴
    - ロードバランシング
    - ヘルスチェック
    - ルーティングポリシー

- 1ホストゾーンにつき$0.5/month

## DNS Record TTL
- Route53から受け取ったDNSクエリをキャッシュとして保管する時間。ブラウザにキャッシュとして保有する時間(TTL)を設定できる
- DNSリクエストがくるとRoute53はIPとTTLをレスポンスとしてクライアントへ返却する

=> TTL:short -> Route53はアクセス数が増えるが、DNSレコードの変更はすぐ反映される

=> TTL:short -> Route53はアクセス数が減るが、DNSレコードの変更はすぐ反映されない

## CNAME vs Alias
- AWS Resource(ALB, CF etc...)は AWS hostnameを持つ
- CNAME:
    - hostnameを他のhostnameに割り当てる
    (ex: abc.com => def.com)
    - **ルートドメイン以外のみ**を対象とする
    (ex: hogehoge.mydomain.com)
    - 課金される
- Alias:
    - hostnameをAWS Resourceに割り当てる(hogehoge.com => foobar.amazonaws.com)
    - **ルートドメインとルートドメイン以外の両方**を対象とする
    (ex: hogehoge.mydomain.com, mydomain.com)
    - 課金されない
    - ネイティブヘルスチェック

## Routing Policy
- Simple
    - 単一リソースへのリダイレクトに使用
    - simple routing policyはヘルスチェックのアタッチが不可能
    - 複数のIPが登録されている時、ランダムな一つの値がクライアントによって選ばれる

- Weighted
    - どのEC2にどのぐらいの割合のリクエストを振り分けるかの設定ができる
    - 最新版のアプリのインスタンスに1%だけトラフィックを振りたい
    - 2つのリージョン間でトラフィックを分けたい
    - ヘルスチェックと併用できる

- Latency 
    - クライアントに近く、レイテンシーの最も低いサーバへリダイレクトする
    - ユーザのレイテンシを改善するのに良い
    - ユーザの所在地の近くのAWSリージョンに基づいてレイテンシは評価される 

-  Failover
    - プライマリインスタンスがunhealtyの時、セカンダリインスタンスに自動で接続する(disaster recovery)
    - Route53のhealty checkが必須

- Geolocation
    - Latency baseとは異なる
    - ユーザの所在地(国)に紐づけたIPへアクセスする(ex:フランス->11.22.33.44, イギリス->22.33.44.55)
    - ロケーションにマッチしない場合、デフォルトポリシーのIPへアクセスする
    
- Multi Value Routing Policy
    - 複数リソースへのトラフィックルーティングに使用
    - レコードとヘルスチェックを併用したい時
    - 最大8のヘルシーレコードがそれぞれのマルチバリュークエリに返される
    - マルチバリューはELBの代替ではない

## Health Checks
- インスタンスがunhealtyならRoute53はリクエストをインスタンスに送らない
- X回のヘルスチェック失敗(デフォルトで3) => unhealty
- X回のヘルスチェックをパス(デフォルトで3) => healty
- デフォルトのヘルスチェックインターバルは30sec(10secも選択可能、コスト高)
- 15のチェッカーがエンドポイントをヘルスチェック
=> 平均1check/2sec
- HTTP, TCP, HTTPSのヘルスチェック対応(SSL証明書不要)
- CloudWatchとヘルスチェックの統合可能
- Route53のDNSクエリにリンク可能

