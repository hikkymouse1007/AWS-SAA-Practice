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

### S3 Security

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

### Block Public Accessの設定
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

### S3でhtmlファイルを公開する方法
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

 ### CORS(Cross-Origin Resource Sharing)
 - オリジン(Origin): scheme(protocol), host(domain), port
 https://www.example.com(protocol:http or https, host:www.example.com, port:80,443)
 - CORS = Cross-Origin Resource Sharing
 - Webブラウザベースのメインオリジンから他のオリジンへのリクエストを許可するメカニズム
 - 同じオリジン:http://www.example.com/app1
&& http://www.example.com/app2
 - 異なるオリジン:http://www.example.com
&& http://other.example.com
- 他のオリジンへのリクエストの許可は、CORSヘッダー(e.g:Access-Controll-Allow-Origin)を使うことで認められる

### S3 CORS
- S3へのcross-originリクエストは、正しいCORSヘッダーが必要
- テストによく出る
- 特定のオリジンまたは全てのオリジン*(all origins)へ許可できる

CORSの設定

![スクリーンショット 2020-12-27 20 29 23](https://user-images.githubusercontent.com/54907440/103170825-ac663c00-488a-11eb-974b-5e218b23c0ef.png)
![スクリーンショット 2020-12-27 19 15 40](https://user-images.githubusercontent.com/54907440/103170828-ae2fff80-488a-11eb-8df6-3490513e40af.png)


