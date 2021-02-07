## Q1 VPC Endpoint
### VPCエンドポイント
https://dev.classmethod.jp/articles/vpc-endpoint-gateway-type/

- Interface Endpoint
プライベートSubからENI経由でエンドポイントに接続して、VPC外部のリソースへアクセス
- Gateway Endpoint
プライベートからSエンドポイントのグローバルIPへ接続し、外部リソースへアクセスS3とDynamoDBのみに適用される

## Q2  Transit Gateway
https://business.ntt-east.co.jp/content/cloudsolution/column-102.html
ネットワークのハブとして、大量のVPCやオンプレミス環境の接続を簡単にしてくれる

>Amazon VPCの数が数百から数千と多くなり、それらを相互に接続していく必要がある場合、ピアリングのための設定や管理運用は煩雑になる恐れがあります。複数のAmazon VPCと複数のオンプレミス拠点とをつなぐ必要が場合にはその煩雑さは一層増すでしょう。

このような大規模な組織のニーズに応え、数百、数千の接続を簡単に構築できるのが、「AWS Transit Gateway」というサービスです。AWS Transit Gatewayを、数多あるAmazon VPCやオンプレミス環境などが接続する、ネットワークの中央に位置したハブのように見立てて管理できます。

Amazon VPC間の相互のピアリングの必要はなく、それぞれをAWS Transit Gatewayに接続するだけで、同じく接続されたほかのAmazon VPCやオンプレミス環境にも接続可能となります。接続先のAmazon VPCやAWS VPN、AWS Direct Connectゲートウェイ、別のAWS Transit Gatewayと柔軟に接続してスケーリングできるのです。

## Q5 CloudTrail
https://docs.aws.amazon.com/ja_jp/awscloudtrail/latest/userguide/how-cloudtrail-works.html

**デフォルトでは Amazon S3 のサーバー側の暗号化 (SSE) を使用して、CloudTrail イベントログファイルが暗号化されます**。ログファイルを AWS Key Management Service (AWS KMS) キーで暗号化する選択もできます。 バケットにログファイルを任意の期間、保存することができます。また、Amazon S3 ライフサイクルのルールを定義して、自動的にログファイルをアーカイブまたは削除することもできます。ログファイルの配信と確認に関する通知が必要な場合は、Amazon SNS 通知を設定できます

## Q7 S3 CORS
AllowedMethodのサポートする値
- GET
- POST
- PUT
- DELETE
- HEAD

## Q10 S3-Cloudfront
CFからのみのアクセス許可を設定するバケットポリシー
![スクリーンショット 2021-02-06 9 13 16](https://user-images.githubusercontent.com/54907440/107101739-abe29d00-685b-11eb-993c-3910418aa68c.png)

## Q11 AWS Direct Connect
**AWS Direct Connect では、転送中のトラフィックは暗号化されません。AWS Direct Connect を通過する転送中のデータを暗号化するには、そのサービスの転送暗号化オプションを使用する必要があります。
AWS Direct Connect および AWS Site-to-Site VPN では、1 つ以上の AWS Direct Connect 専用ネットワーク接続を **Amazon VPC VPN と組み合わせることができます。この組み合わせにより、IPsec で暗号化されたプライベート接続が提供されます。**
https://docs.aws.amazon.com/ja_jp/directconnect/latest/UserGuide/encryption-in-transit.html

## Q13 Security Best Practice
- CloudWatchとCloudTrailによるアラートのモニタリング

# Q14 AWS Redshift
- スナップショットの保有期間
手動スナップショットはいつでも取得できます。デフォルトでは、手動スナップショットは、クラスターを削除した後も、無限に保持されます。手動スナップショットを作成するときに保持期間を指定できます。スナップショットを変更して保持期間を変更することもできます。

https://docs.aws.amazon.com/ja_jp/redshift/latest/mgmt/working-with-snapshots.html#automated-snapshot-schedules

Redshiftとは
https://dev.classmethod.jp/articles/getting-started-redshift-2nd-half/#toc-7

# Q21 EC2 Bastion Host
Private SubnetのEC2へ、管理者のみがアクセスできるSGを設定した、
EC2をおき、そのEC2からPrivateのEC2へSSHするためだけインスタンス(アプリケーションサーバと別に作成する)
https://blog.serverworks.co.jp/tech/2019/08/21/thinkbastion/

## Q23 EC2 Saving Plans

### Compute Savings Plans
- 最も柔軟性があり、コストを最大66％削減するのに役立ちます。これらのプランは、
- インスタンスファミリー、サイズ、AZ、リージョン、OS、テナンシーに関係なく、EC2インスタンスの使用に自動的に適用される
- FargateまたはLambdaの使用にも適用されます。
- Compute Savings Plansを使用すると、C4からM5インスタンスに変更したり、ワークロードをEU（アイルランド）からEU（ロンドン）にシフトしたり、ワークロードをEC2からFargateまたはLambdaにいつでも移動して、自動的に支払いを継続したりできます。

### EC2 Instance Savings Plans

- 最低価格を提供し、リージョン内の個々のインスタンスファミリーの使用（たとえば、バージニア州北部でのM5使用）へのコミットメントと引き換えに最大72％の節約を提供します。(リージョンは固定)
- これにより、AZ、サイズ、OS、テナンシーに関係なく、そのリージョンで選択したインスタンスファミリのコストが自動的に削減されます。
- 同一リージョンのファミリー内のインスタンス間で使用法を変更する柔軟性を提供します。たとえば、Windowsを実行しているc5.xlargeからLinuxを実行しているc5.2xlargeに移行すると、　SavingPlanの価格を自動的に利用できます。

### RDS
- 　オンプレミスかリザーブドのみ

## Q28 DynamoDB
- 分散DB:3つのAZ（アベイラビリティゾーン：要はデータセンター）を跨いでいるので、安心。

https://qiita.com/nh321/items/9f11cff0afc1d31586c7

### Global Table
- Amazon DynamoDB グローバルテーブルは、マルチリージョン、マルチアクティブ データベースをデプロイするための完全マネージド型のソリューションです。独自のレプリケーションソリューションを構築および管理する必要はありません。グローバルテーブルを使用すると、そのテーブルの利用を許可する AWS リージョンを指定できます。DynamoDB では、これらのリージョンに同一のテーブルを作成するのに必要なすべてのタスクを実行し、変更中のデータをすべてのテーブルに伝達します。
- DynamoDB グローバルテーブルは、グローバルに分散したユーザーがいる場合など、大規模にスケールされたアプリケーションを使用する場合に最適です。このような環境では、ユーザーは、非常に高速なアプリケーションパフォーマンスを期待します。グローバルテーブルは、世界中の AWS リージョンに マルチアクティブ の自動レプリケーションを提供します。ユーザーがどこにいても、低レイテンシーでデータを提供することができます
https://docs.aws.amazon.com/ja_jp/amazondynamodb/latest/developerguide/GlobalTables.html

## Q29 AmazonEMR
- Amazon EMR は、AWS でビッグデータフレームワーク (Apache Hadoop や Apache Spark など) の実行を簡素化して、大量のデータを処-理および分析するマネージド型クラスタープラットフォームです。
- これらのフレームワークと関連するオープンソースプロジェクト (Apache Hive や Apache Pig など) を使用することで、分析用のデータやビジネスインテリジェンスワークロードを処理できます。
- さらに、Amazon EMR を使用して大量のデータを変換し、Amazon Simple Storage Service (Amazon S3) や Amazon DynamoDB. などの他の AWS データストアおよびデータベースとの間で移動することもできます。
https://docs.aws.amazon.com/ja_jp/emr/latest/ManagementGuide/emr-what-is-emr.html

### メモ: IISサーバ
IIS（Microsoft Internet Information Services）とは、Microsoftが提供しているWebサーバーサービスです。
ApacheやNginxと同じような系統

## Q33 CloudTrail Validating Log File Integrity
- SSE-KMSを使用した暗号化のサポート– AWS Key Management Service（KMS）キーで暗号化することにより、S3バケットに保存されているCloudTrailログファイルのセキュリティレイヤーを追加できます。CloudTrailは、指定したKMSキーを使用してログファイルを暗号化します。
- ログファイルの整合性の検証– S3バケットに保存されているCloudTrailログファイルの整合性を検証し、CloudTrailがS3バケットに配信した後に削除または変更されたかどうかを検出できます。ログファイルの整合性（LFI）検証は、セキュリティと監査の分野の一部として使用できます。
- CloudTrail が配信した後でログファイルが変更、削除、または変更されなかったかどうかを判断するには、CloudTrail ログファイルの整合性の検証を使用することができます。
- CloudTrail コンソールでログファイルの整合性検証を有効にするには、トレイルを作成または更新するときに、[Enable log file validation] オプションで [Yes] を選択します。
https://aws.amazon.com/jp/blogs/aws/aws-cloudtrail-update-sse-kms-encryption-log-file-integrity-verification/
https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-log-file-validation-intro.html

## Q34 AWS Trusted Advisor
- AWS Trusted Advisorは、「コスト最適化」、「パフォーマンス」、「セキュリティ」、「フォールトトレーランス」の４つの観点から、利用者のAWS環境をAWSが自動で精査し、推奨設定のお知らせをしてくれる機能です。 
https://dev.classmethod.jp/articles/cm-advent-calendar-2015-getting-started-again-aws-td/

## Q38 CloudTrail　グローバルサービスイベント
グローバルサービスイベントを重複して受信しないようにするには、次の点に注意してください。

- デフォルトでは、グローバルサービスイベントは CloudTrail コンソールを使用して作成されたトレイルに配信されます。イベントは、そのトレイルのバケットに配信されます。

- 単一のリージョンのトレイルが複数ある場合は、トレイルを設定し、グローバルサービスイベントがそれらのトレイルの 1 つのみに配信されるようにすることを検討します。詳細については、「グローバルサービスイベントのログ記録の有効化と無効化」を参照してください。

- トレイルの設定をすべてのリージョンのログ記録から単一のリージョンのログ記録に変更すると、そのトレイルに対してグローバルサービスイベントのログ記録は自動的に無効になります。同様に、トレイルの設定を単一のリージョンのログ記録からすべてのリージョンのログ記録に変更すると、そのトレイルに対してグローバルサービスイベントのログ記録は自動的に無効になります。

- 証-跡に対するグローバルサービスイベントのログ記録の変更の詳細については、「グローバルサービスイベントのログ記録の有効化と無効化」を参照してください。

## Q39 CloudFront Query String Parameters
- ウェブアプリケーションによっては、クエリ文字列を使用してオリジンに情報を送信します。
- クエリ文字列はウェブリクエストの一部で、? 文字の後に追加されます。
- この文字列には & 文字で区切られたパラメータを 1 つ以上含めることができます。
- 次の例では、クエリ文字列には 2 つのパラメータ (color=red と size=large) が含まれています。
```
http://d111111abcdef8.cloudfront.net/images/image.jpg?color=red&size=large
```
- RTMP ディストリビューションでは、クエリ文字列パラメータがオリジンに転送されるように CloudFront を設定することはできません。RTMP ディストリビューションがある場合、CloudFront がオリジンサーバーにリクエストを転送する前に、すべてのクエリ文字列パラメータが削除されます。
- クエリ文字列パラメータ間の区切り文字として & 文字を使用する必要があります。
- CloudFront は、クエリ文字列パラメータに基づいてキャッシュを実行する際に、パラメータ名とパラメータ値の大文字と小文字の違いを区別します。次の例は、パラメータ名とパラメータ値の大文字と小文字だけが異なる、同じクエリ文字列です。この場合 CloudFront は、image.jpg に対する 4 つの異なるリクエストをオリジンに転送し、4 つの異なるバージョンのオブジェクトをキャッシュします。
このため、**パラメータ名とパラメータ値の大文字と小文字を統一する (すべて小文字など)** ことをお勧めします。
```
http://d111111abcdef8.cloudfront.net/images/image.jpg?color=red

http://d111111abcdef8.cloudfront.net/images/image.jpg?color=Red

http://d111111abcdef8.cloudfront.net/images/image.jpg?Color=red

http://d111111abcdef8.cloudfront.net/images/image.jpg?Color=Red
```

### メモ
署名付きクッキーとURLの違い
https://infraya.work/exam/awssaa_summary_cloudfront/
署名付きURLのユースケース
- RTMP ディストリビューションを使用する場合。署名付き Cookie は RTMP ディストリビューションではサポートされていません。
- 個別のファイル (アプリケーションのインストールダウンロード) へのアクセスを制限する場合。
- ユーザーが Cookie をサポートしていないクライアント (カスタム HTTP クライアントなど) を使用している場合。

署名付きCookieのユースケース
- 複数の制限されたファイル (HLS 形式の動画のすべてのファイルやウェブサイトの購読者の領域にあるすべてのファイルなど) へのアクセスを提供する場合。
- 現在の URL を変更したくない場合。

RTMP(Real Time Messaging Protocol)
- Adobe が開発している、Adobe Flash プレーヤーとサーバーの間で、音声・動画・データをやりとりするストリーミングのプロトコル。
https://ja.wikipedia.org/wiki/Real_Time_Messaging_Protocol

## Q40 Kinesins DataStream
- Amazon Kinesis Data Streams は、データストリームのデータレコードの保持期間の変更をサポートしています。
- Kinesis data stream はデータレコードの順序付けられたシーケンスで、リアルタイムで書き込みと読み取りができることが前提となっています。
- データレコードはシャードに一時的に保存されます。
- レコードが追加されてからアクセスできなくなるまでの期間は、保持期間と呼ばれます。はデフォルトで **24 時間レコードを保持し、最大 8760 時間 (365 日)** です。
(問題文では168時間だが現在は365日まで)

## Q44 AWS CloudHSM(Hardware Security Module)
### HSM == EBK(Ephemeral Backup Key) & PBK(Permanent Backup Key)
- AWS CloudHSM は、クラウドベースのハードウェアセキュリティモジュール (HSM) です。
- AWS クラウドで暗号化キーを簡単に生成して使用できるようになります。
- CloudHSM で、FIPS 140-2 のレベル 3 認証済みの HSM を使用して、暗号化キーを管理できます。
- データを暗号化するために、HSM はエフェメラルバックアップキー (EBK) として知られる一意の暗号化キーを一時的に使用します。
- EBK は、AWS CloudHSM がバックアップを作成した際に HSM 内で生成される AES 256 ビット暗号化キーです。
- HSM は EBK を生成し、それを使用して NIST special publication 800-38F に準拠する FIPS 承認 AES キーラップメソッドで HSM のデータを暗号化します。
- その後、HSM は暗号化されたデータを AWS CloudHSM に渡します。暗号化されたデータには、EBK の暗号化済みコピーが含まれています。
- EBK を暗号化するために、HSM は永続的バックアップキー (PBK) として知られる別の暗号化キーを使用します。PBK も、AES 256 ビット暗号化キーです。PBK を生成するために、HSM は NIST special publication 800-108 に準拠する FIPS 承認キー取得機能 (KDF) をカウンターモードで使用します。この KDF への入力には次のものがあります。
https://aws.amazon.com/jp/cloudhsm/

### KMS(Key Management System)
- AWS Key Management Service (KMS) を使用することで、暗号化キーを簡単に作成して管理し、幅広い AWS のサービスやアプリケーションでの使用を制御できるようになります。
- AWS KMS はセキュアで弾力性の高いサービスで、キーを保護するために FIPS 140-2 の検証済みまたは検証段階のハードウェアセキュリティモジュールを使用します。
- **AWS KMS は AWS CloudTrail と統合されており**、すべてのキーの使用ログを表示できるため、規制およびコンプライアンスの要求に応えるために役立ちます。
https://qiita.com/ayumi_imai/items/6a74c6327e0f6a1ac7db

■AWS KMS
AWS管理のサーバを共有で利用し、そこで暗号化キーを管理します。
システム的に他の組織へのアクセス制限はされていますが、物理的には同じサーバ上に存在します。
CloudHSMに比べると安価に利用することができます。

■CloudHSM
専用のハードウェアで暗号化キーを保管します。
CloudHSMの方がより安心してキーを管理することが可能です。
KMSに比べると高価です。

## Q48 AWS Polly Lexicon
レキシコンは言語学でいうところの「語彙目録」を意味しており、Amazon Pollyでは「発音レキシコン」として単語の
言い換えや独特の発音を登録・表現することができます。
例えるとパソコンのユーザー辞書に単語を登録し、文章変換時にその単語を表示させるようなものです。
https://docs.aws.amazon.com/ja_jp/polly/latest/dg/managing-lexicons.html
https://recipe.kc-cloud.jp/archives/9294

## Q53 DynamoDB AutoScaling
- Amazon DynamoDB Auto Scaling は AWS Application Auto Scaling サービスを使用し、実際のトラフィックパターンに応じてプロビジョンドスループット性能をユーザーに代わって動的に調節します。
- これにより、テーブルまたは グローバルセカンダリインデックス はプロビジョニングされた読み込みおよび書き込みキャパシティーを増やし、急激なトラフィック増加をスロットリングなしに処理します。
- ワークロードが減ると、Application Auto Scaling はスループットを低下させ、未使用のプロビジョニングされたキャパシティーに料金が発生しないようにします。

- DynamoDB では、次の 2 種類のセカンダリインデックスをサポートしています。
    - グローバルセカンダリインデックス
    テーブルとはパーティションキーまたはパーティション/ソートキーが異なるインデックスです。グローバルセカンダリインデックスは、インデックスに関する クエリが、すべてのパーティションにまたがり、表内のすべての項目を対象とする可能性があるため、「グローバル」と見なされます。
    - ローカルセカンダリインデックス
    テーブルとパーティションキーは同じですが、ソートキーが異なるインデックスです。ローカルセカンダリインデックスは、ローカルセカンダリインデックスの すべてのパーティションの範囲が、同じパーティションキーを持つテーブルパーティションに限定されるという意味で “ローカル” です。

## Q58 AWS SQS CloudWatch Metrix
メトリクスの種類
- ApproximateAgeOfOldestMessage - キューで最も古い削除されていないメッセージのおおよその経過期間。
- ApproximateNumberOfMessagesDelayed - 遅延が発生したため、すぐに読み取ることのできない、キューのメッセージ数。
- ApproximateNumberOfMessagesVisible - キューから取得可能なメッセージの数。
- NumberOfEmptyReceives - メッセージを返さなかった ReceiveMessage API 呼び出しの数
- NumberOfMessagesDeleted - キューから削除されたメッセージの数。
- NumberOfMessagesReceived - ReceiveMessage アクションへの呼び出しで返されたメッセージの数。
- NumberOfMessagesSent - キューに追加されたメッセージの数
- SentMessageSize - キューに追加されたメッセージのサイズ
https://docs.aws.amazon.com/ja_jp/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-available-cloudwatch-metrics.html

## Q59 RDS シャーディング
シャーディングとは
https://pecopla.net/web-column/db-shard#:~:text=%E3%82%B7%E3%83%A3%E3%83%BC%E3%83%87%E3%82%A3%E3%83%B3%E3%82%B0%E3%81%A8%E3%81%AFDataBase,%E8%B2%A0%E8%8D%B7%E5%88%86%E6%95%A3%E3%81%AE%E6%96%B9%E6%B3%95%E3%81%A7%E3%81%99%E3%80%82

## Q62 SQS 
Pub / Subメッセージング
- 「Pub / Subメッセージング」とは、「パブリッシャー（Publisher）」がメッセージを発行し、「サブスクライバー（Subscriber）」がメッセージを受信することで通知する方式です。
- 「トピック」を定義することで、送信先をグルーピングした一斉配信も可能になります。
- トピックを利用すれば、1リクエストでトピックに紐付けられたサブスクライバー全員に送信できるので、複数の送信先に通知するときに有効な方法といえるでしょう。
- 「エンドポイント」（個別のサブスクライバーのこと）に対して個別に通知を送ることも可能です。
- MQTT」と呼ばれるプロトコルを利用すればPub / Subメッセージングサービスを実現できますが、これを1から構築するのはやはり相応のコストがかかってしまうことでしょう。
- 対応プロトコル
    - HTTP
    - SMS
    - HTTPS
    - Email
    - Email-JSON
    - Amazon SQS
    - AWS Lambda

SNSとSQSの連携
https://dev.classmethod.jp/articles/ruby-amazon-sns-sqs/

ファンアウト
https://www.slideshare.net/rs_wisteria/amazon-snssqs-fanout

Amazon MQ
- Amazon MQ はマネージド型メッセージブローカーサービスであり、多くの一般的なメッセージブローカーと互換性があります。
- JMS などの互換性や、AMQP 0--1、AMQP 1.0、MQTT、Amazon MQ、STOMP などのプロトコルに依存する既存のメッセージブローカーからのアプリケーション移行に APIs をお勧めします。OpenWire

- Amazon SQS および Amazon SNS はキューおよびトピックサービスであり、高度にスケーラブルでシンプルに使用でき、メッセージブローカーをセットアップする必要がありません。
- 新規のアプリケーションにはこれらのサービスをお勧めします。ほぼ無制限の拡張性とシンプルな APIs がメリットです。
https://docs.aws.amazon.com/ja_jp/amazon-mq/latest/developer-guide/welcome.html

基本的にMQよりSQSを使うことを推奨

# Q63 S3 Object Lock
- S3 オブジェクトロックでは、Write Once Read Many (WORM) モデルを使用してオブジェクトを保存できます。
- これを使用することで、オブジェクトが固定期間または無期限に削除または上書きされることを防止できます。
- オブジェクトロックを使用すると、WORM ストレージを必要とする規制要件を満たしたり、オブジェクトの変更や削除に対する保護レイヤーを追加したりできます。
https://docs.aws.amazon.com/ja_jp/AmazonS3/latest/dev/object-lock.html


## Q64 S3 Storage Class
- S3 Standard Storage - デフォルトのストレージクラス
- Reduced RedundancyStorage(RRS) - それほど重要ではない再生可能なデータを、S3 標準ストレージクラスより低いレベルの冗長性で保存する
    - => **このストレージクラスの使用は推奨しません。S3 Standardクラスの方がコスト効果に優れています。**
- S3 Standard-IA - 地理的に分離された複数のアベイラビリティーゾーン間でオブジェクトデータを重複して保存します。アクセスが頻繁ではないが、ミリ秒単位のアクセスを必要とする旧いデータに適しています。
-  S3 One Zone-IA - オブジェクトデータを 1 つのアベイラビリティーゾーンでのみ保存するため、S3 標準 – IA よりも安価になります。
https://docs.aws.amazon.com/ja_jp/AmazonS3/latest/dev/storage-class-intro.html