## Subnet - IPv4
- AWSは各サブネットに5つのIPアドレス（最初の4つと最後の1つのIPアドレス）を確保しています。
- これらの5つのIPは使用できず、インスタンスに割り当てることはできません。
- 例）CIDRブロック10.0.0.0/24の場合、予約IPは以下の通りです。
    - 10.0.0.0.0: ネットワークアドレス
    - 10.0.0.1: AWS所有のVPCルータIP
    -  10.0.0.2: アマゾンが提供するDNSマッピング用のAWS所有IP
    - 10.0.0.0.3: 将来の使用のためにAWSが予約
    - 10.0.0.0.255:Network broadcast address. (AWSはVPCでのブロードキャストをサポートしていないため、このアドレスは予約済)
- ExamTip
    - EC2インスタンスに29個のIPアドレスが必要な場合、サブネットサイズ/27（32 IP）を選択することはできません 
    - 少なくとも64 IP、サブネットサイズ/26（64-5 = 59 > 29, 32-5 = 27 < 29）が必要
    => 最低5つ以上のIP数のバッファが必要

![スクリーンショット 2021-01-14 23 51 19](https://user-images.githubusercontent.com/54907440/104608249-022d4780-56c5-11eb-9732-4fc1b4394fa4.png)

![スクリーンショット 2021-01-14 23 56 50](https://user-images.githubusercontent.com/54907440/104607947-b5497100-56c4-11eb-9aeb-1e22249e26f0.png)
![スクリーンショット 2021-01-14 23 59 55](https://user-images.githubusercontent.com/54907440/104607950-b67a9e00-56c4-11eb-95cb-d4d120d16207.png)


![スクリーンショット 2021-01-14 23 58 40](https://user-images.githubusercontent.com/54907440/104608025-cf834f00-56c4-11eb-949c-dc80d2a29a67.png)

![スクリーンショット 2021-01-14 23 38 19](https://user-images.githubusercontent.com/54907440/104607929-afec2680-56c4-11eb-90df-d13fe98c35eb.png)

![スクリーンショット 2021-01-14 23 50 54](https://user-images.githubusercontent.com/54907440/104607934-b1b5ea00-56c4-11eb-8ea5-4fe87d24d72e.png)

## 5つの使用不可能なIP
![スクリーンショット 2021-01-15 0 05 00](https://user-images.githubusercontent.com/54907440/104608595-694afc00-56c5-11eb-8949-856e59824821.png)
![スクリーンショット 2021-01-15 0 05 23](https://user-images.githubusercontent.com/54907440/104608604-6b14bf80-56c5-11eb-8247-5421412dd229.png)

## EC2 x VPC
- サブネットにパブリックIPを割り当てる

![スクリーンショット 2021-01-15 0 41 24](https://user-images.githubusercontent.com/54907440/104613850-ffcdec00-56ca-11eb-9eda-1f8e646ff68b.png)
![スクリーンショット 2021-01-15 0 42 17](https://user-images.githubusercontent.com/54907440/104613856-00ff1900-56cb-11eb-93e2-6798b0439403.png)
![スクリーンショット 2021-01-15 0 44 33](https://user-images.githubusercontent.com/54907440/104613860-0197af80-56cb-11eb-8008-47946f3b7956.png)
サブネットを設定しただけでは、EC2にSSHできない
=> InternetGateway+αの設定が必要