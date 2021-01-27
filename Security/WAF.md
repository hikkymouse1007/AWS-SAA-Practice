# WAF
## AWS WAF - ウェブアプリケーションファイアウォール
- ウェブアプリケーションを一般的なウェブ攻撃から保護します。
- レイヤ7はHTTP(対レイヤ4はTCP)
- Application Load Balancer、API Gateway、CloudFrontへのデプロイ
- Web ACL (Web アクセス制御リスト) を定義します。
- ルールには以下のものを含めることができます。IP アドレス、HTTP ヘッダー、HTTP ボディ、または URI 文字列
- 一般的な攻撃 - SQLインジェクションとクロスサイトスクリプティング（XSS）からの保護 
- サイズ制約、ジオマッチ（ブロック国
- レートベースのルール（イベントの発生をカウントする） - DDoS 防御用

## AWS Firewall Manager
- AWS Organizasionの全アカウントのルールを管理する
- 共通のセキュリティルール
- WAFルール（Application Load Balancer、API Gateways、CloudFront）
- AWS Shield Advanced（ALB、CLB、Elastic IP、CloudFront
- VPCのEC2とENIリソースのセキュリティグループ