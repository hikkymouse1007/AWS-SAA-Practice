# VPCのDNS解決
- DNSサポートの有効化(=DNS解決の設定) - DefaultTrue (= DNS解決の設定)
    - デフォルトでTrue
    ![スクリーンショット 2021-01-18 22 02 49](https://user-images.githubusercontent.com/54907440/104919201-35c7e480-59d9-11eb-80c1-856eb539b48b.png)
    - DNS解決がVPCでサポートされているかどうかを判断するのに役立つ
    - True の場合、AWS DNS サーバを "169.254.169.253" にクエリする
- DNSホスト名の有効化: (= DNSホスト名の設定)
    - 新規作成されたVPCのデフォルトはFalse、デフォルトのVPCのデフォルトはTrueです。
    ![スクリーンショット 2021-01-18 22 03 11](https://user-images.githubusercontent.com/54907440/104919206-3791a800-59d9-11eb-9f3a-c9426aa5e962.png)
    - enableDnsSupport=trueにしなければ無効
    - もしTrueならば、パブリックEC2インスタンスにパブリックホスト名を割り当てます。
    ![スクリーンショット 2021-01-18 22 04 06](https://user-images.githubusercontent.com/54907440/104919209-38c2d500-59d9-11eb-9fde-cf74c1568079.png)
- Route 53 のプライベートゾーンでカスタム DNS ドメイン名を使用する場合は、これらの属性を両方とも true に設定する必要がある


PublicインスタンスからPrivateインスタンスの
```
[ec2-user@ip-10-0-0-22 ~]$ dig demo.foobar.internal

; <<>> DiG 9.11.4-P2-RedHat-9.11.4-26.P2.amzn2.2 <<>> demo.foobar.internal
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 23921
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;demo.foobar.internal.		IN	A

;; ANSWER SECTION:
demo.foobar.internal.	300	IN	CNAME	www.google.com.
www.google.com.		187	IN	A	172.217.161.68

;; Query time: 3 msec
;; SERVER: 10.0.0.2#53(10.0.0.2)
;; WHEN: 月  1月 18 13:39:46 UTC 2021
;; MSG SIZE  rcvd: 93
```

![スクリーンショット 2021-01-18 22 41 12](https://user-images.githubusercontent.com/54907440/104922821-8c83ed00-59de-11eb-97d8-13785be2820c.png)
![スクリーンショット 2021-01-18 22 16 21](https://user-images.githubusercontent.com/54907440/104922827-8e4db080-59de-11eb-9616-b7e992bb4487.png)

参考資料:
https://dev.classmethod.jp/articles/dig-route53-begginer/


# Network ACLs(Access Controll Lists) & SG Incoming Request
詳しく:https://qiita.com/TaigoKuriyama/items/d5b63bf7b169f4b8ffbd

リクエストがインバウンドルールをパスした場合、アウトバウンドルールは無条件にパスされる(stateful)。

ネットワークＡＣＬ
- NACLは、サブネットからとサブネットへのトラフィックを制御するファイアウォールのようなもの
- デフォルトの NACL は、すべてのアウトバウンドとインバウンドを許可
- サブネットごとに1つのNACL、新しいサブネットにはデフォルトのNACLが割り当てられる
- NACLルールの定義
    - ルールには番号(1-32766)があり、低い番号の方が優先順位が高くなる
    - 例: #100 ALLOW <IP> と #200 DENY <IP> を定義した場合、IP は許可される(100[優先] < 200)
    - 最後のルールはアスタリスク (*) であり、ルールが一致しない場合はリクエストを拒否する
    - AWSでは100単位でのルール追加を推奨
- 新しく作られたNACLは全てを否定する
- NACLは、サブネットレベルで特定のIPをブロックするのに最適

以下の設定では、EC2のSGで80アクセスを許可しても、NACLsのルールによりアクセス許可される。
![スクリーンショット 2021-01-18 23 15 31](https://user-images.githubusercontent.com/54907440/104926286-4da46600-59e3-11eb-936a-a591c5820268.png)
![スクリーンショット 2021-01-18 23 16 58](https://user-images.githubusercontent.com/54907440/104926294-4f6e2980-59e3-11eb-8c47-05b466279a3f.png)

- エフェメラルポートについて
https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html#nacl-ephemeral-ports

