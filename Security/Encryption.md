## Encryption in flight (SSL)
- データは送信前に暗号化され、受信後に復号化
- SSL証明書は、暗号化（HTTPS）に役立つ
- 送信中の暗号化は、MITM（中間者攻撃）が起こらないことを保証

## Server side encryption at rest
- サーバで受信したデータは暗号化される
- データは送信前に復号化される
- キー（通常はデータキー）によって暗号化された形で保存される
- 暗号化/復号化キーはどこかで管理する必要があ李、サーバーはキーにアクセスしなければならない


## Client side encryption
- データはクライアントによって暗号化され、サーバによって復号化されない
- データは受信側のクライアントによって復号化される
- サーバはデータを復号化すべきでない
- エンベロープ暗号化(Envelope Encryption)の利用が可能

## KMS
- AWSサービスの暗号化のほとんどはKMS
 - 簡単にデータへのアクセスを制御する方法、AWSは私たちのために鍵を管理します。
- IAM for Authrizationとの完全に統合
- 以下のサービスとシームレスに統合:
    - Amazon EBS: encrypt volumes
    - Amazon S3: Server side encryption of objects • Amazon Redshift: encryption of data
    - Amazon RDS: encryption of data
    - Amazon SSM: Parameter store
    - Etc...
- CLIとSDKの使用も可能

## KMS – Customer Master Key (CMK)Types
- 対称型 (AES-256 鍵)
    - KMSの最初の提供、暗号化と復号化に使用される単一の暗号化キー
    - KMSと連携しているAWSサービスではSymmetric CMKを利用している
    - エンベロープの暗号化に必要
    - 暗号化されていない鍵にはアクセスできません（使用するにはKMS APIを呼び出す必要があります）。
- 非対称 (RSA & ECC 鍵ペア)
    - 公開鍵（暗号化）と秘密鍵（復号化）のペア
    - 暗号化/復号化、署名/検証の操作に使用します。
    - 公開鍵はダウンロード可能ですが、秘密鍵は暗号化されていない状態でアクセスします。
    - 利用事例：KMS APIを呼べないユーザーによるAWS外での暗号化

## SSM Parameter Store
- 設定と秘密のための安全なストレージ 
- オプションでKMSを使用したシームレスな暗号化
- サーバーレス、スケーラブル、耐久性、簡単なSDK
- 設定/秘密のバージョン追跡
- パス＆IAMを使った構成管理 
- CloudWatchイベントで通知
- CloudFormationとの統合