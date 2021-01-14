# CIDR((Classless Inter-Domain Routing)) - IPv4
## Basics
- CIDRはセキュリティグループのルール、またはAWSネットワーク全般に使用される
- IP アドレスの範囲を定義するのに役立ちます。
    - WW.XX.YY.ZZ/32 : 1つの固定IP
    - 0.0.0.0.0/0 : 全てのIP
    - IPレンジの定義192.168.0.0.0/26: 192.168.0.0.0 - 192.168.0.63 (64 IP)
- CIDR:以下の二つのコンポーネント
    - ベースIP (XX.XX.XX.XX.XX)
    - サブネットマスク (/26)
- ベースIP:レンジ内に含まれるIP
- サブネットマスク:IP内で変更できるビット数を定義
    - サブネットマスクは2つの形式で表現可能。ex)
        - 255.255.255.255.0 :一般的でない表記
        - 24                :一般的な表記

## Subnet Masks(固定IPレンジを表現)
• /32 allows for 1 IP = 2^0
• /31 allows for 2 IP = 2^1
• /30 allows for 4 IP = 2^2
• /29 allows for 8 IP = 2^3
• /28 allows for 16 IP = 2^4
• /27 allows for 32 IP = 2^5
• /26 allows for 64 IP = 2^6
• /25 allows for 128 IP = 2^7
• /24 allows for 256 IP = 2^8
• /16 allows for 65,536 IP = 2^16 
• /0  allows for all IPs = 2^32

• Quick memo:
    • /32 – no IP number can change
    • /24 - last IP number can change
    • /16 – last IP two numbers can change 
    • /8 – last IP three numbers can change 
    • /0 – all IP numbers can change

## Example
• 192.168.0.0/24 = ... ?
    • 192.168.0.0 – 192.168.0.255 (256 IP)
• 192.168.0.0/16 = ... ?
    • 192.168.0.0 – 192.168.255.255 (65,536 IP)
• 134.56.78.123/32 = ... ? 
    • Just 134.56.78.123
• 0.0.0.0/0 
    • All IP

IPADressGuid: https://www.ipaddressguide.com/cidr
 
## Private vs Public IP(IPv4) Allowed ranges
- IANA(Internet Assigned Numbers Authority)は、プライベート(LAN)アドレスとパブリック(インターネット)アドレスを使用するためのIPV4アドレスの特定のブロックを確立
- プライベートIPは、特定の値のみを許可
    - 10.0.0.0.0 - 10.255.255.255 (10.0.0.0.0/8) <= 大規模ネットワーク
    - 172.16.0.0.0 - 172.31.255.255 (172.16.0.0.0/12) <= AWSのデフォルト
    - 192.168.0.0.0 - 192.168.255.255 (192.168.0.0.0/16) <= ex) 自宅ネットワーク
- インターネット上の残りのIPはすべてパブリックIP(=上の予約済みプライベートのIPレンジは使用できない)


## VPC in AWS - IPv4
- VPC =Virtual Private Cloud
- 1つのリージョンに複数のVPCを持つことができます（1つのリージョンにつき最大5個まで - ソフトリミット)
- VPCあたりの最大CIDRは5
    - 各CIDRに対して 
        - 最小サイズは/28 = 16 IPアドレス
        - 最大サイズは/16 = 65536 IPアドレス
- VPCはプライベートなので、以下のプライベートIPの範囲のみが許可
    -  10.0.0.0 – 10.255.255.255 (10.0.0.0/8)
    -  172.16.0.0 – 172.31.255.255 (172.16.0.0/12)
    -  192.168.0.0 – 192.168.255.255 (192.168.0.0/16)
- VPCのCIDRは、他のネットワーク（例：社内）と重複しないようにする

## コンソール
- デフォルトのVPC
![スクリーンショット 2021-01-14 22 53 56](https://user-images.githubusercontent.com/54907440/104600280-23d60100-56bc-11eb-8d26-999234d9a1e1.png)

![スクリーンショット 2021-01-14 22 53 45](https://user-images.githubusercontent.com/54907440/104599961-bfb33d00-56bb-11eb-928c-de19b0ef2253.png)


- サブネットは3つ用意されているが、利用可能なIPv4アドレス数は通常より5つ少ない
　
![スクリーンショット 2021-01-14 22 54 54](https://user-images.githubusercontent.com/54907440/104599968-c2159700-56bb-11eb-9400-efe85d81abbd.png)　
![スクリーンショット 2021-01-14 22 55 27](https://user-images.githubusercontent.com/54907440/104599992-c93ca500-56bb-11eb-8a0c-69d9eb1a3e0f.png)

## VPCハンズオン

![スクリーンショット 2021-01-14 23 36 41](https://user-images.githubusercontent.com/54907440/104605129-736afb80-56c1-11eb-91ef-6c0d1d81e6ee.png)

![スクリーンショット 2021-01-14 23 38 19](https://user-images.githubusercontent.com/54907440/104605268-9f867c80-56c1-11eb-84c5-c94c61629cae.png)

- テナンシーとは
https://dev.classmethod.jp/articles/ec2-tenancy/
