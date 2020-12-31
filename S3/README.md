# S3
## Buckets
- buckets:directory, objects:files
- バケットネームはグローバルユニーク
- リージョンレベルで定義される
- Naming convention
    - 大文字禁止
    - アンダースコア禁止
    - 3-63文字
    - IP禁止
    - 小文字または数字からスタート
- Object
    - キーを持つ
    - キーはフルパス
        - s3://my-bucket/file.txt
        - s3://my-bucket/folder/file.txt
    - キーはプレフィックスとオブジェクト名
        - s3://my-bucket/folder/file.txt
    - バケット内にはディレクトリの概念はない
    - キーは/を含む長い名前から成る
    - 最大5TBのオブジェクトまで入る
    - 5GB以上のデータは分割アップロードされる
    - Metadata
    - Tags
    - Vesrion ID

## バージョニング
- S3のファイルはバージョニング可能
- バケットレベルで可能
- 同一キーの上書きはバージョンのインクリメントをする
- バージョニングによって、不意の削除から保護&バージョンの復元が可能

## S3 Encryption for Object
SSE(ServerSideEncryption
- SSE-S3
    - S3マネージドキーによる暗号化
    - オブジェクトはサーバサイドで暗号化
    - AES-256暗号化タイプ
    - リクエストheaderに以下の設定が必要
    
        “x-amz-server-side-encryption": "AES256"
- SSE-KMS
    - KMS(KeyManagementSystem)による暗号化
    - ユーザコントロールとAuditTrail(監査証跡)によるKMSの利点
    - オブジェクトはサーバサイドで暗号化
    - リクエストheaderに以下の設定が必要
    
        “x-amz-server-side-encryption": ”aws:kms"

- SSE-C
    - AWSのサービス外のデータキーを使ったサーバサイド暗号化
    - S3は暗号化キーを保管しない
    - HTTPS化必須
    - 全てのHTTPヘッダーに暗号化キーが必要

- Client Side Encryption
    - S3 Encryption Clientなどのクライアントライブラリを使う
    - S3に送信する前にクライアント側での暗号化が必要
    - S3からデータを取得する時、非暗号化が必要
    - クライアントサイドで完全にキーと暗号化のマネジメントを行う

### Encryption in transit(SSL/TLS)
- S3は以下のEPへ解放されている
    - 暗号化されていないHTTPエンドポイント
    - 暗号化されたHTTPSエンドポイント
- HTTPSは必須ではないが推奨
- ほとんどのクライアントはデフォルトでHTTPS
- SSE-CではHTTPSが必須

## S3 Security

- User based
 - IAMポリシー :IAMにより呼び出し可能なAPIをユーザに割り当てる
 - Resource Based
    - バケットポリシー
    - オブジェクトアクセスコントロールリスト: 詳細な設定
    - バケットアクセスコントロールリスト: 一般的ではない

**Note**
    - ユーザIAM permissionが許可しているか、リソースポリシーが許可している場合にIAMユーザ(IAM principal)はオブジェクトにアクセスできる
    - または明示的な拒否設定がされていない時にアクセスできる

## S3 Bucket Policy
- JSONベース
    - Resources: バケットとオブジェクト
    - Actions: 許可または拒否するAPI
    - Effect: Allow/Deny
    - Principal ポリシーの適応対象ユーザ/アカウント
- S3バケットポリシーの目的
    - パブリックアクセスの付与
    - アップロード時のオブジェクト暗号化強制
    - クロスアカウントアクセスの付与

## Block Public Accessの設定
- 以下の設定によってバケットとオブジェクトのアクセス制限を付与
    - 新しいアクセスコントロールリスト(ACLs)
    - 全てのアクセスコントロールリスト(ACLs)
    - 新しいパブリックバケットまたはアクセスポイントポリシー
- パブリックバケットまたはアクセスポイントポリシーによってパブリックアクセス、クロスアカウントアクセスの制限

- これらの設定によって機密データの外部流出を防ぐ
- アカウントレベルでの設定が可能

## Securityその他
- Network 
    - VPC対応
- ロギングと監視
    - 他のS3バケットにS3のアクセスログを保存可能
    - AWS CloudTrailによってAPIの履歴が確認できる
- User Security
 - MFA Delete: バージョニングされたバケットのオブジェクト削除にMFAの要求が可能
 - Pre Signed URLs: 有効期限つきURLの設定ができる

## S3でhtmlファイルを公開する方法
- アクセス許可でパブリックアクセスのブロックをオフにする
- AWS Policy Generatorでポリシーを設定する
    - Type of Policy:S3 BucketPolicy
    - Effect: Allow
    - Principal: *
    
    (*: all users)
    - Actions: GetObject
    - ARN: arn:aws:s3:::hikky-brand-new-bucket/* 

    (* : all resources in S3 bucket)

```
# Policy JSON
{
  "Id": "Policy1609064348075",
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Stmt1609064343228",
      "Action": [
        "s3:GetObject"
      ],
      "Effect": "Allow",
      "Resource": "arn:aws:s3:::hikky-brand-new-bucket/*",
      "Principal": "*"
    }
  ]
}
```

 ## CORS(Cross-Origin Resource Sharing)
 - オリジン(Origin): scheme(protocol), host(domain), port
 https://www.example.com(protocol:http or https, host:www.example.com, port:80,443)
 - CORS = Cross-Origin Resource Sharing
 - Webブラウザベースのメインオリジンから他のオリジンへのリクエストを許可するメカニズム
 - 同じオリジン:http://www.example.com/app1
&& http://www.example.com/app2
 - 異なるオリジン:http://www.example.com
&& http://other.example.com
- 他のオリジンへのリクエストの許可は、CORSヘッダー(e.g:Access-Controll-Allow-Origin)を使うことで認められる

## S3 CORS
- S3へのcross-originリクエストは、正しいCORSヘッダーが必要
- テストによく出る
- 特定のオリジンまたは全てのオリジン*(all origins)へ許可できる

CORSの設定

![スクリーンショット 2020-12-27 20 29 23](https://user-images.githubusercontent.com/54907440/103170825-ac663c00-488a-11eb-974b-5e218b23c0ef.png)
![スクリーンショット 2020-12-27 19 15 40](https://user-images.githubusercontent.com/54907440/103170828-ae2fff80-488a-11eb-8df6-3490513e40af.png)

## S3 MFA Delete
- S3の重要な作業を行う前にMFAをかけることができる
MFA (multi factor authentication) forces user to generate a code on a device (usually a mobile phone or hardware) before doing important operations on S3
- MFAを使用するために、S3のバージョニング設定が可能
- 以下の操作にMFAが必要
    - オブジェクトのバージョンの恒久的デリート 
    - バケットのバージョニング停止

- 以下の操作にはバージョニング不要
    - バージョニングの有効化
    - 削除済みのバージョンのリスト化
    - バケットオーナー(rootアカウント)のみがMFAの有効・無効化の設定が可能
    - MFA-DeleteはCLIからのみ(現在)

## S3 Access Log
- S3への許可・拒否された全てのアクセスログは、他のS3バケットへロギングされる。
- アクセスログは、ログ解析ツールによって解析可能
- Athenaを使った解析
- ログフォーマット
    https://docs.aws.amazon.com/AmazonS3/latest/dev/LogFormat.html

**注意**:ログバケットを監視対象バケットにしないこと。
    =>ロギングループが発生する

## 設定
ログの保管先対象のS3を設定する
プレフィックスをつけておくと、プレフィックス付きでログを保管してくれる
<img width="880" alt="スクリーンショット 2020-12-31 10 06 01" src="https://user-images.githubusercontent.com/54907440/103389165-8e316200-4b50-11eb-8d93-2336b93c365a.png">

## S3 Replication
- バケットのバージョニングが必要
- CRR(Cross Region Replication)

    異なる二つのリージョン間でレプリカを作成

    ユースケース
    - コンプライアンス(情報の保護)
    - 低遅延アクセス
    - アカウント間のレプリケーション
- SRR(Same Region Replication)
    
    同一リージョン内でレプリカを作成
    ユースケース
    - ログの集約
    - 本番アカウントとテストアカウント間のライブレプリケーション

- バケットは異なるアカウントに設置可能
    
- コピーは非同期で行われる
- 適切なIAMの設定が必要

- レプリケーションの有効化後に追加されたオブジェクトのみがコピー対象
- オブジェクトの削除に関する事項
    - バージョンIDなしの削除は、削除マーカを追加し、複製されない
    - バージョンIDありの削除は、ソースレベルで削除し、複製されない

    =>　削除アクションは対象のバケットのみに適応される

- 複製の連鎖は不可能
        
        - バケット1: バケット2への複製

        - バケット2: バケット3への複製

        => 1を2に複製しても3には複製されない 

## S3 pre-signed URLs
- SDKまたはCLIを使用して事前署名されたURLを生成できます
    - ダウンロード用（設定楽,CLIを使用）
    - アップロード用（設定難,SDK推奨）

- 有効期限はデフォルトで3600秒、--expires-in [TIME_BY_SECONDS]の引数でタイムアウトを変更できる

- 事前に署名されたURLを与えられたユーザーは、GET / PUTのURLを生成した人のアクセス許可を継承する

- ユースケース：
    - ログインしたユーザーのみがS3バケットにプレミアムビデオをダウンロードできるようにする
    - URLを動的に生成することにより、動的に変化するユーザーのリストにファイルをダウンロードを許可する
    - ユーザーにバケット内の正確な場所にファイルをアップロードを一時的に許可する

- 認証済みURL

    オブジェクトアクションの開くから開くことのできるURLは、認証済みのURL
<img width="1143" alt="スクリーンショット 2020-12-31 11 08 38" src="https://user-images.githubusercontent.com/54907440/103390619-cc328400-4b58-11eb-909e-328f8a10b89a.png">

    一方、オブジェクトURLはパブリックアクセスを許可していなければ開くことができない
<img width="1065" alt="スクリーンショット 2020-12-31 11 09 34" src="https://user-images.githubusercontent.com/54907440/103390622-cfc60b00-4b58-11eb-8b26-bcd5912050a7.png">

```
# 認証済みURLの発行
$ aws s3 presign s3://mybucket/myobject --region my-region


# 有効期限の設定オプション
$ aws s3 presign s3://mybucket/myobject  --expires-in 300 --region my-region

# Example
$ aws s3 presign s3://s3-mashimo-origin/beach.jpg --region ap-northeast-1 --expires-in 300                                        
https://s3-mashimo-origin.s3.amazonaws.com/beach.jpg?AWSAccessKeyId=AKIAVJVK73SJQR52LRVJ&Signature=FK%2F%2BXzsY6iBi8SJ1DY11p7%2BgdnA%3D&Expires=1609381461

# Issueがあった場合
# 暗号化されたファイルのURLを生成するときに問題が発生しないように、適切な署名バージョンを設定する
$ aws configure set default.s3.signature_version s3v4

```

## S3 Storage Class
種類
- Amazon S3 Standard - General Purpose
    - 複数のAZにわたるオブジェクトの高い耐久性
    - AmazonS3で10,000,000個のオブジェクトを保存する場合、平均して10000年に1回1つのオブジェクトが失われると予想できます。
    - 1年間通して99%の可用性
    - 2つの同時施設障害耐性
    - ユースケース：ビッグデータ分析、モバイルおよびゲームアプリケーション、コンテンツ配信...
-  Amazon S3 Standard-Infrequent Access (IA) 
    - アクセス頻度は低いが、必要に応じて迅速にアクセスする必要があるデータに適する
    - 複数のAZにわたるオブジェクトの高い耐久性（99.999999999％）
    - AmazonS3Standardと比較して低コスト
    - 2つの同時施設障害耐性
    - ユースケース：ディザスタリカバリのデータストアとして、バックアップ
- Amazon S3 One Zone-Infrequent Access
    - 特徴はIAと同じだが、データは単一のAZに保存される
    - 単一のAZ内のオブジェクトの高い耐久性（99.999999999％） AZが破壊されるとデータが失われる
    - 99.5％の可用性
    - 低遅延と高スループットのパフォーマンス
    - IAと比較して低コスト（20％）
    - 転送時のデータと保存時の暗号化にSSLをサポート
    - ユースケース：オンプレミスデータのセカンダリバックアップコピーを保存する、または再作成できるデータを保存する
- Amazon S3 Intelligent Tiering
    - S3Standardと同じ低遅延と高スループットのパフォーマンス
    - 少額の月額監視および自動階層化料金
    - アクセスパターンの変更に基づいて、2つのアクセス層間でオブジェクトを自動的に移動します
    - 複数のアベイラビリティーゾーンにわたるオブジェクトの99.999999999％の耐久性を実現するように設計されています
    - アベイラビリティーゾーン全体に影響を与えるイベントに対する回復力
    - 特定の年に99.9％の可用性を実現するように設計されています
- Amazon Glacier
    - アーカイブ/バックアップを目的とした低コストのオブジェクトストレージ
    - データは長期間（数十年）保持
    - オンプレミスの磁気テープストレージの代替
    - 平均年間耐久性は99.999999999％
    - 1か月あたりのストレージあたりのコスト（$ 0.004 / GB）+取得コスト
    - Glacierの各アイテムは「アーカイブ」と呼ばれる（最大40TB）
    - アーカイブは「Vaults(金庫)」に保存されます

- Amazon Glacier Deep Archive
    - Amazon Glacier – 3つの復元オプション: 
        - Expedited (1 to 5 minutes)
        - Standard (3 to 5 hours)
        - Bulk (5 to 12 hours)
        - 最小ストレージ期間は90日
    - Amazon Glacier Deep Archive – 長期間用ストレージ – 安価:
        - Standard (12 hours)
        - Bulk (48 hours)
        - 最小ストレージ期間は180日
        - Amazon S3 Reduced Redundancy Storage (deprecated - omitted)

## S3 Lifecycle Rules
- Transition Action：
オブジェクトがいつ別のストレージクラスに移行されるかを定義する
    - 作成から60日後にオブジェクトを標準IAクラスに移動します 
    - 6か月後にアーカイブするためにGlacierに移動します
    
-  Expiration Action：しばらくすると期限切れになる（削除する）ようにオブジェクトを構成します
    - アクセスログファイルは、365日後に削除するように設定できます
    - 古いバージョンのファイルを削除するために使用できます（バージョン管理が有効になっている場合）
    - 不完全なマルチパートアップロードを削除するために使用できます
- 特定のプレフィックスに対してルールを作成可能（例-s3：// mybucket / mp3 / *）
- 特定のオブジェクトタグに対してルールを作成可能（例- Department：Finance）

## S3-Scenario1

 >EC2上のアプリケーションは、プロフィール写真がAmazon S3にアップロードされた後、画像のサムネイルを作成する。サムネイルは簡単に復元することができ、45日間保存する必要がある。この場合の設計についてはどうすべきか
- S3のソース画像はSTANDARDのままで、45日後にGLACIERに移行するようにライフサイクルを設定する。
- S3のサムネイルをONEZONE_IA上に置き、45日後に有効期限が切れる(削除する)ようにライフサイクルを設定する。
## S3-Scenario2
>会社のルールで、削除したS3オブジェクトを15日間はすぐに復旧できるよう必要があるが、レアケースである。15日経過後、最大365日間は削除されたオブジェクトが48時間以内に復旧できる必要がある。
- オブジェクトのバージョン管理は、S3のバージョニングを有効にする必要があるので、「削除されたオブジェクト」は実際には「削除マーカー」によって隠されており、復元することができる。
- 「非現行バージョン」のオブジェクトはS3_IAに移行することができる
- これらの「非現行バージョン」は後から"ディープアーカイヴ"へ移動させることができる

## Athena
- S3ファイルに対して直接分析を実行するサーバーレスサービス
- SQLを使用したファイルの分析。
- JDBC / ODBCドライバを所有
- 実行したクエリとスキャンしたデータ量に対しての従量課金
- CSV, JSON, ORC, Avro, Parquet をサポート (Presto 上に構築)
- ex: ビジネスインテリジェンス/分析/レポーティング、分析、VPCフローログ、ELBログ、CloudTrailトレイルなどの分析。
- S3で直接分析を行うにはAthenaを使う