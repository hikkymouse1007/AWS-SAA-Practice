## Q1 S3とライフサイクル
- マルチパートアップロード
>S3 は最大5TBまでのオブジェクト（ファイル）を保管できるのですが、保存のため一度に送信できるサイズは5GBという制限があります。 ではどうやって5TBのオブジェクトを保存するのか？というと aws cli や SDK を使って小さく分割したファイルを送信し、全ての部品が揃ったら S3 で一つのファイルに復元されます。 この機能を「マルチパートアップロード」といいます。
このマルチパートアップロードが失敗した時に残ってしまう「部分ファイル」はマネジメントコンソールから見ることも消すこともできませんので、 見えないファイルにも関わらず容量あたりの利用料金は掛かっています。

aws cli を使い、以下のコマンドを実行することで不完全なマルチパートを一覧表示できます。
```
aws s3api list-multipart-uploads --bucket バケット名
```

***「ライフサイクルポリシー」の設定*** により自動で無駄の原因となるファイルを削除することが可能です。 

Ref:
- https://dev.classmethod.jp/articles/sugano-045-s3-lifecycle-policy/
- https://aws.amazon.com/jp/premiumsupport/knowledge-center/s3-multipart-cleanup-lifecycle-rule/

## Q4 SQSとRDS
RDSへの書き込みが集中した場合、SQSを利用してDBへの書き込みキューを入れることで、
データを損なわずに確実にRDSの書き込みを成功させることができる。

## Q5 LambdaとDynamoDBと権限
Lambdaに以下のポリシーを持つIAMロールをアタッチすることでアクセスを許可する

```
AmazonDynamoDBFullAccess
AWSLambdaDynamoDBExecutionRole
```

## Q6 S3 Glacier Deep Archive (incorrect)
- S3のアーカイブ階層について
>S3 Intelligent-Tieringは、アクセスパターンに基づいてコスト効率の高いストレージ階層に自動で移動してくれる機能です。アクセス頻度を読めないときに設定することで、運用負荷を上げることなくコストを最適化できます。

Glacier Deep Archiveからの別のストレージクラスへのオブジェクト移動方法は以下
- Amazon S3コンソールを使用して元の場所に復元
- ライフサイクルポリシーを使用してオブジェクトを必要なS3-Intelligent-Tieringストレージクラスに移動

- >S3 Glacier または S3 Glacier Deep Archive ストレージクラスにアーカイブしたオブジェクトは、リアルタイムでアクセスできません。まず復元リクエストを開始してから、リクエストで指定した期間 (日数) 中にオブジェクトの一時コピーが利用できるようになるまで待ちます。

最小ストレージ期間: 30日未満の保管でも30日分の保管としてみなすよ、というようなルール

    - Amazon S3 Glacier — データの一部を数分で取得する必要があるアーカイブに使用します。
        - Expedited (1 to 5 minutes)
        - Standard (3 to 5 hours)
        - Bulk (5 to 12 hours)
        - 最小ストレージ期間は90日
    - Amazon Glacier Deep Archive S3 Glacier Deep Archive— ほとんどアクセスする必要がないデータのアーカイブに使用します。
        - Standard (12 hours)
        - Bulk (48 hours)
        - 最小ストレージ期間は180日
        - Amazon S3 Reduced Redundancy Storage (deprecated - omitted)

Ref:
- https://dev.classmethod.jp/articles/s3-intelligent-tiering-archive-tier/
- https://docs.aws.amazon.com/ja_jp/AmazonS3/latest/dev/restoring-objects.html
- https://docs.aws.amazon.com/ja_jp/AmazonS3/latest/dev/storage-class-intro.html
- https://tech-dive.xyz/2018/10/29/post-122/

## Q7 S3 Glacier Select (incorrect)
Glacier上のデータに対してクエリを実行し、結果をS3に出力してくれます。
サーバーなどは不要です。

>Amazon S3 Glacier Selectは、Amazon S3 Glacierから直接データをクエリできます。このデータをクエリするために、S3バケットへのデータの復元は必要ありません。

- Amazon S3 Selectを使用すると、単純なSQLを使用してS3バケットに保存されているオブジェクトからデータのサブセットをクエリできます。
Ref:
https://dev.classmethod.jp/articles/how-to-use-glacier-select/

## Q8 S3 Server-Side Encryption (incorrect)
- Amazon S3 Select が出力できるのは JSON 出力形式のみです。
- Amazon S3 Select は、GZIP または Snappy を使用した列指向の圧縮のみをサポートしています。
- これを使用するには、オブジェクトをCSV、JSON、またはApacheParquet形式のS3バケットに保存する必要があります。
- GZIPおよびBZIP2圧縮は、サーバー側の暗号化を使用したCSVまたはJSON形式でサポートされています。
https://dev.classmethod.jp/articles/running-s3-encryption/

## Q9 S3 Object-Versioning (incorrect)
オブジェクトのバージョニングは同一のバケット内で実行可能
- バージョン対応のS3バケットを使用すると、オブジェクトのバージョンごとに異なる保持期間を設定できます。

## Q10 AWS Managed Microsoft Active Directory(AD)

- AWS Direct Connect はオンプレミスから AWS への専用ネットワーク接続の構築をシンプルにするクラウドサービスソリューションです。AWS Direct Connect を使用すると、AWS とデータセンター、オフィス、またはコロケーション環境との間にプライベート接続を確立することができます。

 >アクティブディレクトリとはWindowsパソコンの機能やユーザー情報を管理するために、Windows Serverに設けられた機能のことです。Active Directoryはディレクトリサービスと呼ばれるものの1つで、Windows 2000から導入されています。
Active Directoryを使うことで、システム管理者はパソコンに関する様々な情報や状態を一括して管理することができ、手間を大幅に軽減することが可能です。
ちなみにActive DirectoryはWindows Serverに標準に搭載された機能です。そのため専用のソフトウェアを購入する必要はありせん。
https://cybersecurity-jp.com/software-tool/31169

- AWSマネージドMicrosoftADはオンプレ・クラウドの両方で使用可能
- 会社がAWSサービスに接続できるようにするには、VPNまたはDirect Connectを実装すると、AWSマネージドMicrosoftADをクラウドサービスとオンプレミスサービスの両方に使用できるようになります。(自社のオンプレからクラウドへ接続できるかの確認は必要)

![microsoft-ad](https://user-images.githubusercontent.com/54907440/106372128-025d6080-63b0-11eb-8d39-58dd47cb414d.png)

## Q11 Route53 + CloudFront (incorrect)


- 代表的なレコード
    - A: hostname to IPV4
    - AAAA: hostname to IPV6
    - CNAME: hostname to hostname
    - Alias: hostname to AWS resource
https://zenn.dev/naoki_mochizuki/articles/d0d9fd2459250baeb37d

### CNAME補足

例えば、hoge.jpというドメインに対して
CNAMEでfuga.jpが指定されている
fuga.jpには、Aレコードで11.11.11.11というIPアドレスが設定されている
となった際に、
```
hoge.jp → fuga.jp → 11.11.11.11
```
という経路を辿って、最終的には11.11.11.11サーバのコンテンツを取得することが可能となるのです。

エイリアスレコードには、IPアドレスやドメイン名の代わりに、CloudFrontディストリビューション、Elastic Beanstalk環境、ELB(CLB,ALB,NLB),静的ウェブサイトとして設定されたAmazon S3バケット、または同じホストゾーンにある別のRoute53レコードが登録可能

## Q12 Route53 + CloudFront (incorrect)
- iSCSI（Internet Small Computer System Interface）
TCP/IPネットワーク上でSCSI接続を利用可能にする仕組みです。iSCSIを利用することローカルストレージ同然にネットワーク上のストレージを利用することが可能となります。
また、iSCSI等によって構築されたストレージ専用ネットワークをSAN（※）と呼びます。
https://qiita.com/OPySPGcLYpJE0Tc/items/be9daae23b80478b81ff

>ファイルゲートウェイ – ファイルゲートウェイ は、Amazon Simple Storage Service (Amazon S3) へのファイルインターフェイスをサポートし、サービスと仮想ソフトウェアアプライアンスを組み合わせます。この組み合わせを使用すると、ネットワークファイルシステム (NFS) やサーバーメッセージブロック (SMB) などの業界標準のファイルプロトコルを使用して、Amazon S3 でオブジェクトを保存および取得できます。

>ボリュームゲートウェイ – ボリュームゲートウェイは、オンプレミスのアプリケーションサーバーから iSCSI (Internet Small Computer System Interface) デバイスとしてマウントできる、クラウドベースのストレージボリュームを提供します。

>テープゲートウェイ – テープゲートウェイは、クラウドベースの仮想テープストレージを提供します。テープ・ゲートウェイは、 VMware ESXi、KVM、または Microsoft Hyper-V ハイパーバイザ。

テープゲートウェイを使用すると、バックアップデータをコスト効果や耐久性の高い方法で GLACIER または DEEP_ARCHIVE にアーカイブできます。テープゲートウェイは仮想テープインフラストラクチャとして、お客様事業での需要に応じシームレスにスケーリングができ、物理テープインフラストラクチャのプロビジョニング、スケーリング、保守といった運用の負担を解消します。

https://docs.aws.amazon.com/ja_jp/storagegateway/latest/userguide/WhatIsStorageGateway.html

## Q14 FSx for Lustre (incorrect)
- FSx forLustreは高性能ストレージです。S3からデータを読み取り、同時に複数のインスタンスに接続できます。

> スパコン(HPC)でなくてはならないコンポーネントの一つが並列ファイルシステムです。
並列ファイルシステムがなぜ重要なのかは「一週間でなれる！スパコンプログラマ」を読んで頂くとして、様々なファイルシステムが存在するなかで、スーパーコンピュータ「富岳」含め、採用事例が豊富なのが Lustre です。
https://dev.classmethod.jp/articles/getting-started-with-amazon-fsx-for-lustre/

* DynamoDB acceleratorはDynamoDBのインメモリキャッシュ

## Q14 FSx for Lustre (incorrect)
EC2インスタンスを事前にウォームアップするには、EC2Hibernateを使用できます。インスタンスは、
Amazon **EBSルートボリューム**で起動する必要があります。

## Q20 Saving Plans (incorrect)
>Compute Savings Plans は、最も優れた柔軟性を提供し、コストを**最大 66% 削減**するために役立ちます。これらのプランは、インスタンスファミリー、サイズ、アベイラビリティーゾーン、リージョン、OS、またはテナンシーに関わらず EC2 インスタンスの使用に自動で適用されます。また、Fargate を使用する場合にも適用されます。例えば、Compute Savings Plans では、C4 インスタンスから M5 インスタンスへの変更、欧州 (アイルランド) から欧州 (ロンドン) へのワークロードのシフト、または EC2 から Fargate へのワークロードの移動をいつでも行えます。Savings Plans の料金は、引き続き自動的に支払うことができます。

>EC2 Instance Savings Plans は**料金が最も低く**、リージョン内の個々のインスタンスファミリーの契約 (例: バージニア北部で M5 の使用量) と引き換えに、**最大 72%** の節約を提供します。これは、アベイラビリティーゾーン、サイズ、OS、またはテナンシーに関わらず、そのリージョン内で選択されたインスタンスファミリーのコストを自動的に削減します。EC2 Instance Savings Plans は、そのリージョンのファミリー内におけるインスタンス間で使用量を変更する柔軟性を提供します。例えば、Windows を実行する c5.xlarge から Linux を実行する c5.2xlarge に移動しても、自動で Savings Plan 料金の恩恵を受けることができます。


https://aws.amazon.com/jp/savingsplans/pricing/

## Q22 EBS & AMI (incorrect)
- EBSはスナップショットからコピーする必要がある(EBSそのものから他のリージョンに直接コピーはできない)
- AMIとEBSの違い
![AMI](https://user-images.githubusercontent.com/54907440/106373169-376eb080-63ba-11eb-8e38-9cbae5436dc9.png)
![snapshot](https://user-images.githubusercontent.com/54907440/106373171-39d10a80-63ba-11eb-9017-ab5a705cef42.png)
https://www.mussyu1204.com/wordpress/it/?p=739#:~:text=AMI%E3%82%A4%E3%83%A1%E3%83%BC%E3%82%B8%E4%BD%9C%E6%88%90%E3%81%A8%E3%82%B9%E3%83%8A%E3%83%83%E3%83%97%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88%E4%BD%9C%E6%88%90%E3%81%AE%E9%81%95%E3%81%84&text=EBS%E3%82%B9%E3%83%8A%E3%83%83%E3%83%97%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88%E3%81%AFS3,%E4%B8%A1%E6%96%B9%E3%81%8C%E5%8F%96%E5%BE%97%E3%81%95%E3%82%8C%E3%81%BE%E3%81%99%E3%80%82

## Q23 AWS Global Accelerator (incorrecr)
- AWS Global Acceleratorは、エンドポイントとして</br>
**2つの静的IP**を提供します。
- AWS Global Acceleratorは、AWSグローバルネットワークを介してトラフィックをエンドポイントに転送することにより、アプリケーションのパフォーマンスも向上させます。
- AWS CloudFrontは、アプリケーションに静的IPを提供しません。

- CloudFrontとの違い
https://dev.classmethod.jp/articles/aws-summit-online-2020-day2-track2-1530-1600-cloudfront-globalaccelerator-globalnetwork/

## Q24 AWS Glacier Vault
>VaultとはS3でいうBucketに当たるもので、**ファイルをGlacierに格納する際には必ずVaultを作り、その中にファイルを入れます。** Glacier(氷河）の中で食べ物を保管するにはVault(貯蔵庫)に入れておくのが一番、ということですね。
https://dev.classmethod.jp/articles/introduce-of-glacier-vault-access-policy/

## Q26 Data Migration
- DataSync
    - AWS DataSync は、オンプレミスストレージシステムと AWS ストレージサービス間で、および AWS ストレージサービス間でのデータの移動を簡素化、自動化、および高速化するオンラインデータ転送サービスです。
    - 主にNFSサーバーorServer Message Block (SMB)をS3、EFS、Fsxなどに移行するために使用されるサービスです。
    - **データベースの移行はサポートしていない**

- Server Migration Service(SMS)
    - AWS Server Migrationサービスは、オンプレミスのワークロードをAWSEC2に移行するために使用されます。
    - **データベースの移行はサポートしていない**
- Database Migration Service(DMS)
    - AWS Database Migration Serviceは、オンプレミスデータベースをAWSに移行し、移行中にオンプレミスデータベースを完全に機能させるのに役立ちます。
    - **移行先はAWS RDS**
- AWS Migration Hub
    - AWS Migration Hub では、AWS およびパートナーの複数のソリューション間における**アプリケーション移行の進行状況を 1 つの場所で追跡できます**。Migration Hub を使用すると、ニーズに最も適する AWS およびパートナーの移行ツールを選択でき、アプリケーションのポートフォリオ全体で移行状態の可視性が得られます。
- Server Migration Service Connector
    - AWS SMSを利用するには、AWS Server Migration Service Connectorが必要になります。AWS SMSConnecterは、移行をする際に利用する一時的な仮想マシンの環境のことです。AWS SMS ConnecterはオンプレミスのVMware環境上で立ち上げる必要があります。
    - SMSを使うときの機能
    - **目的はサーバの移行**

## Q28 DataStreaming
- App Stream
    - デスクトップアプリケーションを任意のコンピュータに配信
    - 会社のアプリにリモートでアクセスしたいなど
    - VPNを使わず会社のアプリに接続できる
    - ブラウザがあれば使用可能
https://note.com/tmt_tty/n/nc5fc97318a86
https://aws.amazon.com/jp/appstream2/?nc2=type_a&blog-posts-cards.sort-by=item.additionalFields.createdDate&blog-posts-cards.sort-order=desc

- Data Pipeline
    - 指定された間隔で、信頼性のあるデータ処理やデータ移動（AWS のコンピューティングサービスやストレージサービス、ならびにオンプレミスのデータソース間）を行うことができるウェブサービス
    - 保存場所にあるユーザのデータに定期的にアクセスし、そのスケールで変換と処理を行い、その結果を Amazon S3、Amazon RDS、Amazon DynamoDB、Amazon Elastic MapReduce（EMR）のような AWS サービスに効率的に転送できる
    https://dev.classmethod.jp/articles/cm-advent-calendar-2015-getting-started-again-datapipeline/

## Q29 AWS Directory Service
>AWS Directory Service は、既存の Microsoft AD やライトウェイトディレクトリアクセスプロトコル (LDAP) –対応のアプリケーションをクラウド上で使用するユーザー向けに複数のディレクトリオプションを提供します。また、開発者がディレクトリを通じてユーザー、グループ、デバイス、およびアクセスを管理する場合にも、同じオプションを提供します。
https://www.youtube.com/watch?v=mpnN-wCrRZs

### 種類
- Managed Microsoft Active Directory
    - AWS Directory Service for Microsoft Active Directory は、AWS Managed Microsoft AD とも呼ばれ、実際の Microsoft Windows Server Active Directory (AD) を搭載し、AWS クラウド上で AWS によって管理されます。これを使用すると、さまざまな Active Directory 対応アプリケーションを – クラウドに移行できます。
- Simple AD
    - Simple AD は、Samba 4 を使用する の Microsoft Active Directory 互換AWS Directory Serviceディレクトリです。Simple AD は、ユーザーアカウント、グループメンバーシップ、Linux ドメインまたは Windows ベースの EC2 インスタンスの結合、Kerberos ベースの SSO、グループポリシーなどの基本的な Active Directory 機能をサポートしています。AWS では、このサービスの一環として、モニタリング、日次スナップショット、および回復を提供します。
- AD Connector
    - AD Connector は、互換性のある AWS アプリケーション (Amazon WorkSpaces、Amazon QuickSight、Amazon EC2 for Windows Server など) をオンプレミスの既存の Microsoft Active Directory に簡単に接続する方法を提供するプロキシサービスです。
- Cognito

## Q33 AWS Serverless Application Service(SAM)
- AWSサーバーレスアプリケーションモデル（AWS SAM）は、AWSでサーバーレスアプリケーションを構築するために使用できるオープンソースフレームワークです。
    - https://docs.aws.amazon.com/ja_jp/serverless-application-model/latest/developerguide/what-is-sam.html
    - https://qiita.com/spring_i/items/e087905a82c40cf900a0
- SAMテンプレートというYamlファイルで管理可能
- 比較として、Serverless Framrworkが挙げられる
    - https://qiita.com/KizashiTakata/items/ff6cf3a9cf60c2cfd4f8
- ElasticBeanstalkはサーバレス用途ではない

## Q34 AWS Directory Service(incorrect)
- Simple ADは、AWS DirectoryService の**MicrosoftActiveDirectory互換ディレクトリ**です。
- MicrosoftADは機能が冗長。最低限の機能であればSimpleADで十分。
https://aws.amazon.com/jp/opsworks/stacks/

## Q36 AWS OpsWorks
- AWS OpsWorks Stacksを使用すると、AWSおよびオンプレミスのアプリケーションとサーバーを管理できます。
- Chefベースのアプリケーション管理サービスで、起動・設定・デプロイ・アンデプロイ・停止といった事を管理することができ、
OpsWorksを利用することで、セットアップやデプロイ時の人的ミスや工数負荷を減らす、といった運用の自動化をすることが出来ます。
- Chefと異なりセットアップなしにGUI上で設定するだけで利用可能な事が魅力で、既存のChefのレシピをそのまま利用することも可能です。

>OpsWorksで構築・管理する環境は、大別すると「Stack」「Layer」「Instance」「App」という4つの要素で構成されています。Instance、Appについては名前からも分かるように、それぞれ1つのEC2 Instance（1台のサーバ）、1つのアプリケーションを指しています。その一方で、StackやLayerについては耳慣れない用語かもしれません。Layerは、例えばWeb Server、App Server、DB Serverといった、役割ごとのEC2 Instanceを作成するための雛形となる設定情報をまとめたものです。OpsWorksではこのLayerに設定された情報を元に、EC2 Instanceのセットアップを行います。そしてStackは、各Layer、Instance、Appなどをすべて束ねた、これから作成する環境一式を指す概念です。Stackでは環境全体で共有する設定情報を保持しています。

https://aws.amazon.com/jp/opsworks/stacks/

## Q38 AWS Organizational Units
- アカウントのすべての一元管理AWS
    - 既存のアカウントを組織に結合して、アカウントを一元管理することができます。
    - 自動的に組織の一部であるアカウントを作成し、他のアカウントを組織に招待することができます。
    - アカウントの一部または全部に影響するポリシーをアタッチすることもできます。

- すべてのメンバーアカウントの一括請求
    - 一括請求は AWS Organizations の機能です。組織の管理アカウント (以前の「マスターアカウント」) を使用して、すべてのメンバーアカウントを統合し、支払うことができます。
     - 一括請求 (コンソリデーティッドビリング) では、管理アカウントは、組織内のメンバーアカウントの請求情報、アカウント情報、アカウントアクティビティにアクセスすることもできます。
     - この情報は、Cost Explorer などのサービスに使用され、管理アカウントが組織のコストパフォーマンスを向上させるのに役立ちます。

- 予算、セキュリティ、コンプライアンスのニーズを満たすアカウントの階層的なグループ化
    - アカウントを組織単位 (OU) にグループ化し、各 OU に異なるアクセスポリシーをアタッチすることができます。
    - たとえば、特定の規制要件を満たす AWS サービスにのみアクセスする必要があるアカウントがある場合、それらのアカウントを 1 つの OU に入れることができます。
    - その後、それらの規制要件を満たさないサービスへのアクセスをブロックする OU にポリシーをアタッチすることができます。
    - 他の OUs 内で OUs を 5 レベルの深さにネストして、アカウントグループの構成方法に柔軟性を持たせることができます。

- 各アカウントがアクセスできる AWS サービスと API アクションを一元管理するポリシー
    - 組織の管理アカウントの管理者は、サービスコントロールポリシー (SCP) を使用して、組織内のメンバーアカウントの最大アクセス権限を指定できます。
    - 各メンバーアカウントのユーザーとロールがアクセスできる SCPs サービス、リソース、および個々の API アクションを制限できます。AWSまた、AWS のサービス、リソースおよび API アクションへのアクセスをいつ制限するかの条件も定義できます。
    - これらの制限は、組織内のメンバーアカウントの管理者よりも優先されます。
    - AWS Organizations がメンバーアカウントのサービス、リソース、または API アクションへのアクセスをブロックすると、そのアカウントのユーザーまたはロールはアクセスできません。
    - このブロックは、メンバーアカウントの管理者が IAM ポリシーで明示的にそのようなアクセス許可を付与した場合でも有効なままです。

https://docs.aws.amazon.com/ja_jp/organizations/latest/userguide/orgs_introduction.html

## Q38 AWS Organization how to move accounts
アカウントの移行手順
- 古い Organization からメンバーアカウントを削除する。
- 新しい Organization からメンバーアカウントに招待を送信する。
- メンバーアカウントで新しい Organization への招待を受け入れる。

https://aws.amazon.com/jp/premiumsupport/knowledge-center/organizations-move-accounts/?nc1=h_ls

## Q40 IAM for API Gateway
IAM アクセス許可 を使用して Amazon API Gateway API へのアクセスを制御するには、次の 2 つの API Gateway コンポーネントプロセスへのアクセスを制御します。
- API Gateway で API の作成、デプロイ、管理を行うには、API Gateway の API 管理コンポーネントでサポートされている必要なアクションを行うためのアクセス許可を API デベロッパーに付与する必要があります。
- デプロイされた API を呼び出したり、API キャッシュを更新したりするには、API Gateway の API 実行コンポーネントでサポートされている必要な IAM アクションを行うためのアクセス許可を API の発信者に付与する必要があります。
https://docs.aws.amazon.com/ja_jp/apigateway/latest/developerguide/permissions.html

## Q41 EC2 Config(incorrect)
- EC2 Config == WindowsServer
- **Windows Server 2012 R2 以前用の Windows AMI には、EC2Config サービス (EC2Config.exe) というオプションのサービスが含まれています。** EC2Config は、インスタンスが起動し、起動時にタスクを実行したとき、およびインスタンスを停止または開始するたびに起動します。オンデマンドでタスクを実行させることもできます。タスクには自動的に有効化されるものもありますが、手動で有効化しなければならないものもあります。使用は任意ですが、このサービスは他の手段では利用できない高度な機能を提供します。このサービスは LocalSystem アカウントで実行されます。

- EC2 metadata

```
curl http://169.254.169.254/latest/meta-data/
ami-id
ami-launch-index
ami-manifest-path
block-device-mapping/
events/
hostname
instance-action
instance-id
instance-type
local-hostname
local-ipv4
mac
metrics/
network/
placement/
profile
public-hostname
public-ipv4
public-keys/
reservation-id
security-groups
services/
```

## Q43 Data Monitoring
- Amazon CloudWatchは、RDSインスタンスからのIOPSメトリックスを監視するために使用されます。
- アラームがトリガーされた場合、Amazon Simple NotificationServiceを使用して通知を送信します。


## Q47 Organizations Move Accounts
- ：マスターアカウントの場合、アカウントが別のアカウントへの招待を受け入れる前に、組織を削除する必要があります。


## Q51 Consolidated Billing
- この機能を利用すると、請求機能を共有できますが、AWS Organizations の高度な機能は含まれません。たとえば、ポリシーを使用して、異なるアカウントのユーザーとロールが実行できることを制限することはできません。高度な AWS Organizations 機能を使用するには、組織のすべての機能を有効にする必要があります。
https://docs.aws.amazon.com/ja_jp/organizations/latest/userguide/orgs_getting-started_concepts.html#feature-set-cb-only

## CF Origin Access Identity(OAI)
- オリジンアクセスアイデンティティ (OAI) と呼ばれる特別な CloudFront ユーザーを作成し、ディストリビューションに関連付けます。
- CloudFront が OAI を使用してバケット内のファイルにアクセスしてユーザーに提供できるように、S3 バケットのアクセス許可を設定します。ユーザーが S3 バケットへのダイレクト URL を使用して、そこにあるファイルにアクセスできないようにしてください。
- CloudFrontの署名付きURLを使ってコンテンツへのアクセスを制限する場合、CloudFrontを介さずに直接S3にアクセスされては意味がありません。このため「CloudFront経由に限りS3バケットにアクセス出来ることとし、CloudFrontを経由しない場合はS3バケットにアクセス出来ない」ように設定します。


https://dev.classmethod.jp/articles/cf-s3-deliveries-use-signurl/
https://docs.aws.amazon.com/ja_jp/AmazonCloudFront/latest/DeveloperGuide/private-content-restricting-access-to-s3.html
https://docs.aws.amazon.com/ja_jp/AmazonCloudFront/latest/DeveloperGuide/PrivateContent.html

