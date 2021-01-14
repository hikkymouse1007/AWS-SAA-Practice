# Internet Gateway(IGW)
- インターネットゲートウェイは、VPCインスタンスのインターネット接続を補助
 - 水平方向スケール対応、HA・冗長化
- VPCとは別に作成する
- OneVPCは1つのIGWにのみ接続でき、その逆も可能
- インターネットゲートウェイは、パブリックIPv4を持つインスタンスのNATでもある 
- **インターネットゲートウェイ単体ではインターネットに接続できない**
- **ルートテーブルも編集する必要がある**

作成したIGWはVPCにアタッチする
![スクリーンショット 2021-01-15 0 58 34](https://user-images.githubusercontent.com/54907440/104615853-fba2ce00-56cc-11eb-9dfa-a6b0b74765f3.png)
![スクリーンショット 2021-01-15 0 58 49](https://user-images.githubusercontent.com/54907440/104615863-fe052800-56cc-11eb-85db-861643f6b7d1.png)
=> まだこの時点でEC2へアクセスできない
=> ルートテーブルの設定が必要

## ルートテーブル
- VPCにはデフォルトで1つメインルートテーブルが作成されているが、管理しづらいので、PublicとPrivate用のルートテーブルをそれぞれ作成する

![スクリーンショット 2021-01-15 1 13 33](https://user-images.githubusercontent.com/54907440/104618209-c2b82880-56cf-11eb-8def-e38bcb3ea36d.png)

- パブリックルートテーブルには2つのパブリックサブネットをアタッチする

![スクリーンショット 2021-01-15 1 15 17](https://user-images.githubusercontent.com/54907440/104618218-c481ec00-56cf-11eb-8d5d-10d2cbe45798.png)

- プライベートルートテーブルには2つのプライベートサブネットをアタッチする

![スクリーンショット 2021-01-15 1 15 34](https://user-images.githubusercontent.com/54907440/104618222-c5b31900-56cf-11eb-9b24-4f749dfd92df.png)
![スクリーンショット 2021-01-15 1 15 50](https://user-images.githubusercontent.com/54907440/104618227-c6e44600-56cf-11eb-85d5-bdf5a50ce4a5.png)

- プライベートテーブルはローカルネットワークだけの通信ルートを設定する

![スクリーンショット 2021-01-15 1 16 46](https://user-images.githubusercontent.com/54907440/104618228-c77cdc80-56cf-11eb-90ff-ade33899bc9e.png)

- パブリックテーブルはローカルネットワーク+外部通信(グローバルネットワーク:0.0.0.0/0)のためのIGWの通信ルートを設定する

![スクリーンショット 2021-01-15 1 17 42](https://user-images.githubusercontent.com/54907440/104618231-c8157300-56cf-11eb-807d-9b08a1d9b02b.png)
![スクリーンショット 2021-01-15 1 17 55](https://user-images.githubusercontent.com/54907440/104618235-c8ae0980-56cf-11eb-943d-716078082566.png)