## RDS
### Type
- Postgres
- MySQL
- MariaDB
- Oracle
- Microsoft SQL Server
- Aurora

### Advantage
- 自動プロビジョニング
- 継続的バックアップ
- ダッシュボードモニタリング
- マルチAZセットアップ
- スケーリング可能(水平・垂直)
- EBSストレージバックアップ
=> SSH接続はできない

### 自動バックアップ
- デイリーバックアップ
- 5minごとのログバックアップ
- 7days retention
- スナップショット
    - ユーザの手動トリガー
    - バックアップからの手動再取得

### RDS read replica 
- 5read replicaの作成
- AZ, CrossAZ, またはCross Regionの設置が可能
- **非同期**によるレプリケーション可能(リアルタイムに画面には反映されない)
- アプリケーションからのデータ読み取り専用のレプリカとして動作する
- アプリからのデータ書きこみ、更新、削除はマスターのみ可能
- マルチAZでReadReplicaを作成するときは課金されるが、同一AZにReadReplicaを作成する場合は、無料
- マルチAZはDisasterRecovery用として使用できる。一方でスケーリング用ではなく、あくまでバックアップとして使う。

### SQL Electron
GUIでDBにアクセスできる
https://sqlectron.github.io/

### RDSの暗号化
- AWS KMS AES-256によってマスターとリードDBを暗号化できる
- 暗号化の設定はRDSのローンチする時には行う
- masterが暗号化されていなければ、readは暗号化できない
- TDE(Transparent Data Encryption)はOracleとSQLサーバのみ使用可能

- 暗号化したRDSのバックアップ
    - 暗号化されていないスナップショットのバックアップは暗号化されていない
    - 暗号化されたRDSのバックアップは暗号化されている
    - コピーから暗号化されたスナップショットを作成できる

- 非暗号化RDSの暗号化
 - 暗号化されていないDBのスナップショットを作成
 - スナップショットのコピーを作成し、暗号化する
 - 暗号化されたスナップショットからDBを作成
 - アプリを新しいDBにマイグレートし、古いDBを削除する

### Network & IAM
- Network Security
    - RDSはプライベートサブネットにデプロイする
    - EC2と同様にSGを設定できる
- Access Management
    -  IAMポリシーによりRDSの操作権限を設定できる
    - ログインユーザ名とパスワードによってDBログイン可能
    - IAMベースの認証はMySQLとPostgreSQLに使用される

### RDS- IAM Authentication
- IAMベースの認証はMySQLとPostgreSQLに使用される
- IAMとRDSのAPIから認証トークンの取得が可能
- トークンのlifetimeは15分
- 利点
    - Network通信はSSLによる暗号化が必要
    - DBの代わりにIAMによるユーザ管理が可能
    - IAMロールとEC2プロフィールの統合が簡単

### Amazon Aurora
- AuroraはAWSの技術、(not OSS)
- PostgreSQLとMySQLのみが対応
- MySQLの5倍のパフォーマンス、PostgreSQLno
3倍のパフォーマンス
- Auroraのストレージは10GBから64TBまで自動スケーリングする
- 15レプリカの所有可能(MySQLは5個まで), レプリケーションタイムが速い(10msで複製)
- フェイルオーバーが速い(復旧)

### Aurora Serverless
- 使用量に合わせて自動スケーリング
- 突然の大量アクセスにも向いている

### Global Aurora
- Aurora Cross Region Read Replica
    - ディザスタリカバリーに向いている
    - 設定がシンプル
- Aurora Global Database(recommended)
    - 1つのプライマリリージョン(read/writer)
    - 最大5つのセカンダリリージョン(read only)、複製ラグは1秒未満
    - 最大16Read-onlyレプリカをセカンダリリージョンに作成可能
    - レイテンシー削減の補助
    - 一分以内にディザスタリカバリー用の追加リージョン設定

### Elasticache
- RDSのキャッシュサービス
- RedisまたはMemcached
- キャッシュ=
 高パフォーマンスかつ低レイテンシのインメモリDB
- DBへのアクセス負荷を低減させる
- シャードを使った書き込みスケーリング
- Readレプリカによる読み取りスケーリング
- フェイルオーバキャパシティ対策のマルチAZ
- AWSによるOSメンテナンス~リカバリ

### DB Cache
- キャッシュにデータを見に行く
- データがあればデータを返し、なければDBへデータを見に行く。読み込んだデータはキャッシュへ書き込み
### User Session Store
- ユーザのAppへのログイン
- キャッシュにセッション書き込み
- 他のインスタンスにアクセスした時、キャッシュにセッションを読み込みに行く

### Redis vs Memchached
- Redis
    - Multi AZ と自動フェイルオーバー
    - Readレプリカによる高アベイラビリティ
    - AOFによるデータの高耐久性
        >AOF (Append Only File)
    AOFはデータベースのWAL/REDOログに似た書き込みログで、すべての書き込みコマンドが追記で記録されます。 ... AOFファイルはRedisの起動時に自動的に読み込まれます。 AOFファイルとRDBファイルの両方が存在する場合はAOFファイルが優先されます。
    - バックアップと復元

- Memcached
    - マルチノードによるデータパーティショニング
    >パーティショニングとはデータを複数に分割して格納することです。 データを分割することにより、性能や運用性が向上し、故障の影響を局所化することができます。 巨大なデータを扱う場合にはパーティショニング機能を利用することも検討してみてください。
    - 非持続性
    - バックアップと復元不可
    - マルチスレッド設計

### Cache Security
- Redis & Memcached
    - SSL対応
    - IAM認証非対応
    - ElastiCacheのIAMポリシーはAWS APIレベルセキュリティにのみ使われる

- Redis Auth
    - Redis Cluserにパスワード/トークンの設定可能
    - 追加レベルのセキュリティ機能
- Memcached
    - SASLベースの認証対応

- ElastiCacheパターン
    - Lasy Loading: キャッシュ検索、なければRDS, キャッシュに書き込み
    - Write Through: RDSの更新に合わせてキャッシュも更新
    - Session Store: 一時的なセッションデータをキャッシュに設定する(TTL設定可能)
