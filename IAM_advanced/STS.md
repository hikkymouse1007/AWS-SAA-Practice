## Security Token Service
- AWSリソースへの限定的かつ一時的なアクセスを許可します。
- トークンの有効期限は1時間まで(更新が必要)
- AssumeRole
    - 自分のアカウント内：セキュリティを強化
    - クロスアカウントアクセス：ターゲットアカウントでの役割を想定し、そこでのアクションを実行する 
- AssumeRoleWithSAML
    - SAML でログインしたユーザの認証情報を返す
- AssumeRoleWithWebIdentity
    - IdPでログインしたユーザの認証情報を返す (Facebookログイン、Googleログイン、OIDC対応など) 
- AWSはこれを使わず、代わりにCognitoを使うことを推奨
- GetSessionToken
    - ユーザーまたはAWSアカウントのrootユーザーからのMFAを対象

## Identity Federation in AWS
- フェデレーションでは、AWS以外のユーザーがAWSのリソースにアクセスするためのユーザーの一時的な役割を担うことができます。
- ユーザーは、IDが提供するアクセスの役割が付与されることを想定
- フェデレーションには様々な種類がある
    - SAML 2.0
    - カスタムアイデンティティブローカー
    - Amazon CognitoとのWebIdentityFederation(WIF)
    - Amazon Cognitoを使わないWIF
    - シングルサインオン
    - Non-SAML with AWS Microsoft AD
- フェデレーションを使えばIAMユーザーを作る必要がない（ユーザー管理はAWSの外にある

## SAML 2.0
SAML 2.0 フェデレーション
- AWS（または任意のSAML 2.0）とActive Directory / ADFS(Active Directory Federation Service)を統合する
- AWSコンソールまたはCLIへのアクセスを一時的な認証情報を介して提供する 
- 従業員ごとにIAMユーザーを作成する必要はありません。

## Cognito
- Goal
    - クライアント側(モバイル、Webアプリ)からのAWSリソースへの直接アクセスを提供する
- Example 
    - Facebook ログインを使用して S3 バケットに書き込むための (一時的な) アクセス権を提供する
- Problem
    - アプリにIAMユーザーを作成したくない
- How
    - federated identity providerにログインする
    - Federated Identity Poolから一時的にAWSの資格情報を取り戻す
    - これらのクレデンシャルには、事前に定義されたIAMポリシーが付属しており、そのパーミッションが記載されています。

## AWS Organization
- グローバルサービス
- 複数のAWSアカウントの管理が可能
- メインアカウントはマスターアカウントです。
- その他のアカウントは会員アカウント
- 会員アカウントは1つの組織の一部としてのみ利用可能
- すべてのアカウントで統合された請求書 - 単一の支払い方法
- 利用量の集計による価格のメリット（EC2、S3などのボリュームディスカウント 
- APIを利用してAWSのアカウント作成を自動化できる

## Service controlle Policy(SCP)
- IAMアクションのホワイトリストまたはブラックリスト
- OUまたはアカウントレベルでの適用
- マスターアカウントには適用されない
- SCP は、ルートユーザーを含むアカウントのすべてのユーザーとロールに適用される
- SCP はサービス連動ロールに影響を与えない
    - サービス連携ロールにより、他のAWSサービスとAWS Organizationsとの連携が可能になり、SCPによって制限されない
- SCPは明示的な許可設定が必要(デフォルトでは何も許可しない)
- ユースケース
    - 特定のサービスへのアクセスを制限する（例：EMRを使用できない）
    - 明示的にサービスを無効にすることでPCIコンプライアンスを強制する