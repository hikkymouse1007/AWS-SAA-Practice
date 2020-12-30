# SDK Overview
- DynamoDBのようなサービスにコードからアクセスしたい場合がある

- AWS CLIはPython SDK (boto3)を使用している。

- デフォルトリージョンの設定がない場合、us-east-1がデフォルトに設定される。

- デフォルトの認証プロバイダチェインの使用を推奨


- 認証プロバイダチェインは以下の方法で動作:
    - AWS credentials(~/.aws/credentials)

        （自分のPC限定)
    - IAMRoleによるインスタンスプロファイル認証
        
        (for EC2 machines, etc...)
    - 環境変数(非推奨)
    
        (AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY)

-  コードに認証キーを絶対に埋め込まないこと

- 認証のベストプラクティス
    
    上記の方法またはAWSサービス内で使用する場合は、IAMRolesから認証情報を取得する

## APIのリトライ時間    (Exponential Backoff)
APIのリクエストに失敗した場合は、
次のリトライまでのインターバルは
前のリクエストのインターバル2倍かかる

```
1:=> 10ms
2:==> 20ms
3:====> 40ms
4:========> 80ms
5:================> 160ms
```