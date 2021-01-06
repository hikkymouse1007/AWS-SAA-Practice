# API Gateway
- AWS Lambda + API Gateway。管理するインフラなし
 - WebSocketプロトコルをサポート
- API のバージョニング (v1, v2....)
- 環境を変更可能(dev、test、prod...)
- セキュリティ(認証と認可) 
- API キーの作成、リクエストのスロットル処理
- Swagger / Open APIインポートによる高速なAPI定義
- リクエストとレスポンスの変換と検証
- SDKとAPIの仕様を生成
- API レスポンスをキャッシュ

## High-Level Integration
- Lambda Function
    - Lambda Functionの呼び出し
    - AWS LambdaをバックにしたREST APIを簡単に公開する方法
- ＨＴＴＰ
    - バックエンドでHTTPエンドポイントを公開する
    - 例：オンプレミスの内部HTTP API、アプリケーションロードバランサー..
    - レート制限、キャッシング、ユーザー認証、APIキーなどを追加する。
- AWSサービス
    - API Gatewayを介して任意のAWS APIを公開する
    - 例：AWSステップ関数のワークフローを開始し、SQSにメッセージを投稿する
    - 認証の追加、公開デプロイ、レートコントロール...

## Endpoint Types
- Edge-Optimized (default):グローバルクライアント用
    - リクエストはCloudFront Edgeのロケーションを経由してルーティングされる（レイテンシが改善される） 
    - APIゲートウェイはまだ1つのリージョンにしか存在しない
- Regional:
    - 同一地域内のクライアントの場合
    - 手動でCloudFrontと組み合わせることができる（キャッシング戦略と配布をよりコントロールできる）。
- Private:
    - インターフェイス VPC エンドポイント (INI) を使用して VPC からのみアクセス可能
    - リソースポリシーを使用してアクセスを定義する