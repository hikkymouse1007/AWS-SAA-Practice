# CloudFront
- CDN - コンテンツデリバリネットワーク
- 読み取りパフォーマンスが向上し、コンテンツはエッジでキャッシュされる。
- グローバルな216の実在するポイント（エッジロケーション)
- DDoS対策、Shield、AWS Web Application Firewallとの統合
- 外部HTTPSを公開し、内部HTTPSバックエンドと通信可能

## Origin
- S3バケット
    - エッジロケーションでのキャッシュやファイル配布
    - CloudFront Origin Access Identity（OAI）でセキュリティを強化 - CloudFrontをイングレス（S3にファイルをアップロードする）として利用可能
    - S3はエッジロケーションとプライベートネットワーク接続
- カスタムオリジン(HTTP)
    - 以下のサービスをオリジンに指定可能
        - ALB
        - EC2
        - S3 ウェブサイト (S3で静的なWebページを設定する)
        - 任意のHTTPバックエンド
    - EC2の場合は、インスタンスをパブリックネットワークに設置する必要があり、エッジロケーションのIPをSGで許可する必要がある
    - ALBの場合は、ALBはパブリックに設置する必要があるが、EC2はプライベートネットワークに配置することができる。

## CloudFront Geo Restriction
- Distributionへのアクセス制限が可能
- White List:承認された国のリストにある国のいずれかにいる場合にのみ、ユーザーがコンテンツにアクセスできる
- Black List:禁止されている国のブラックリストの国のいずれかにある場合、ユーザーがコンテンツにアクセスできない
- ユーザの所在する国は、サードパーティの Geo-IP データベースを使用して決定される
- Use Case: コンテンツへのアクセスを制御する著作権法

## CloudFront vs S3 Cross Region Replication 
- CloudFront
    - グローバルエッジネットワーク
    - ファイルはTTL（多分1日）の間キャッシュされます。
    - どこでも利用可能な静的コンテンツに最適
- S3
    - レプリケーションを実行したいリージョンごとに設定する必要があります。
    - ファイルはほぼリアルタイムで更新されます
    - 読み取り専用
    - 少ないリージョンで低レイテンシーで利用できる必要があるダイナミックコンテンツに最適

## Memo
S3をオリジンに設定して、CloudFront経由のみでのアクセスを許可した場合、
CloudFrontのURLからのリダイレクトアクセスが有効になるまで3時間程度かかる。

## CloudFront Signed URL / Signed Cookies
- 例えば有料の共有コンテンツを世界中のプレミアムユーザーに配信したいケース
- CloudFront Signed URL / Cookie(署名付きURL/Cookie)を利用する、かつ以下のポリシーを適用する
    - URLの有効期限
    - データへのアクセスを許可するIPレンジ
    - 信頼された署名者（どのAWSアカウントが署名付きURLを作成できるか

- URLの有効期限は？
    - 共有コンテンツ（映画、音楽）：短く（数分程度)
    - 非公開コンテンツ（ユーザーに非公開）：数年

- 署名付きURL = 個々のファイルへのアクセス (1ファイル / 1つの署名付きURL)
- 署名付きクッキー＝複数のファイルへのアクセス（多数のファイル / 1つの署名付きクッキー)

## Origin Access Identity(OAI)
https://docs.aws.amazon.com/ja_jp/AmazonCloudFront/latest/DeveloperGuide/private-content-restricting-access-to-s3.html

## CloudFront Signed URL vs S3 Pre-Signed URL
- CloudFront Signed URL:
    - オリジンではなく、パスへのアクセスを許可する。
    - ルートユーザだけがアカウント全体のキーペアを管理できる
    - IP、パス、日付、有効期限によってフィルタリングできる
    - キャッシング可能

- S3 Pre-Signed URL:
    - URLを事前に署名した人としてリクエストを発行する
    - 署名IAMプリンシパルのIAMキーを使用する
    - ライフタイム制限