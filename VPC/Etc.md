## Egress Only Internet Gateway(Out-Going)
- Egress Only Internet Gateway はIPv6専用
- NATと似たような機能だが、NATはIPv4用(Igress only)
- Good to know: IPv6はすべてパブリックアドレス
- そのため、IPv6 を使用したすべてのインスタンスはパブリックアクセス可能
- Egress Only Internet GatewayはIPv6インスタンスにインターネットへのアクセスを提供するが、インターネットから直接アクセスは不可能
- Egress Only Internet Gatewayを作成した後、ルートテーブルを編集する

## AWS PrivateLink (VPC Endpoint Service)
- 1000台のVPC（自分のアカウントや他のアカウント）にサービスを公開するための最も安全でスケーラブルな方法 
- VPCピアリング、インターネットゲートウェイ、NAT、ルートテーブルなどは必要ありません。
- ネットワークロードバランサー(サービスVPC)とENI(顧客VPC)が必要です。
- NLBが複数AZにあり、ENIが複数AZにある場合、ソリューションはfault tolerate

## Transit Gateway
- 何千ものVPCとオンプレミス間のトランジティブピアリング、ハブアンドスポーク（スター）接続を持つために
- 地域のリソース、地域を超えて働くことができる
- リソースアクセスマネージャ（RAM）を使用したクロスアカウントの共有
- 地域をまたいでトランジットゲートウェイをピアリングすることができます。
- RouteTables: limitwhichVPCcantalkwithotherVPC
- ダイレクトコネクトゲートウェイ、VPN接続に対応
- IPマルチキャストをサポートしています（他のAWSサーバーではサポートされていません