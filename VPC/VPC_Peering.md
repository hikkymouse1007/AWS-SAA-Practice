# VPC Peering
 - AWSのネットワークを利用して、2つのVPCをプライベートで接続する
- 同じネットワークにいるかのように振る舞わせる
- CIDRの重複禁止
- VPCピアリング接続はトランジティブでない（お互いに通信する必要のあるVPCごとにpeeringの設定が必要

ex) VPC_A <-> VPC_Peering <-> VPC_B <-> VPC_Peering <-> VPC_C

この時、VPC_AとVPC_Cは通信できない
- 別のAWSアカウントとVPCピアリングを行うことが可能
- インスタンス間で通信できるように、各VPCサブネットのルートテーブルを更新する必要がある

## Peering Good to Know
- VPCピアリングは、Cross-Region/Accountで動作
- ピアードVPCのセキュリティグループを参照可能(クロスアカウントで動作)
ex)ピアードVPCのSG: sg-8018205XXXXXX/12345688(アカウント番号) => クロスアカウントSGの許可が可能

- ピアリングの設定
接続するVPCのCIDRを割り当てる
![スクリーンショット 2021-01-18 23 51 43](https://user-images.githubusercontent.com/54907440/104931466-ca3a4300-59e9-11eb-89fa-dbb7dd9ed356.png)
![スクリーンショット 2021-01-18 23 47 51](https://user-images.githubusercontent.com/54907440/104931461-c8707f80-59e9-11eb-87a5-72c11802a6cc.png)
- ピアリング接続の承認
![スクリーンショット 2021-01-18 23 48 27](https://user-images.githubusercontent.com/54907440/104931464-c9a1ac80-59e9-11eb-9305-4566fb2289b8.png)
![スクリーンショット 2021-01-18 23 48 16](https://user-images.githubusercontent.com/54907440/104931463-c9091600-59e9-11eb-8398-4531ba79f653.png)



- ルートテーブルの設定
    - 作成したPublicルートテーブル側
    ![スクリーンショット 2021-01-18 23 52 23](https://user-images.githubusercontent.com/54907440/104931449-c3abcb80-59e9-11eb-90db-c5c9a97fcc8c.png)
    - デフォルトのルートテーブル側
    ![スクリーンショット 2021-01-18 23 58 58](https://user-images.githubusercontent.com/54907440/104931451-c5758f00-59e9-11eb-8d5d-9ec59e7dbd74.png)

- EC2内のプライベートIPへのcurl
=> 下のインスタンスから上のインスタンスへのリクエストとレスポンスを確認
<img width="402" alt="スクリーンショット 2021-01-18 23 59 53" src="https://user-images.githubusercontent.com/54907440/104931458-c7d7e900-59e9-11eb-88e9-e7cb0eac9e47.png">


