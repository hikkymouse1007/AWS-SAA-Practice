## SSH
key.pemのパーミッションは**400**にする
ssh ec2-user@54.199.50.230 -i ~/.ssh/key.pem

- ssh接続がタイムアウトした時はSGの設定を見直す
- sshキーのパーミッションはしっかりと確認すること。
- EC2にpub-keyが正しく設置されていて、かつローカルにprivate-keyがあることを確認する。

keyを渡したい時のCLIの設定
https://blog.suzuxander.com/aws/34.html

CLIデプロイ

```
$ aws cloudformation deploy \
    --template-file ./ec2.yaml \
    --stack-name ec2-sample \
    --parameter-overrides \
    KeyName=udemy \
    --profile cf-course
```

削除

```
$ aws cloudformation delete-stack \
    --stack-name ec2-sample \
    --profile cf-course
```

デフォルトユーザの設定

```
export AWS_DEFAULT_PROFILE=cf-course
```

インスタンス情報
引数については以下を参照
https://docs.aws.amazon.com/ja_jp/cli/latest/userguide/cli-services-ec2-instances.html

```
aws ec2 describe-instances 

aws ec2 describe-instances --instance-ids i-054e75d635943e9fb 
```

インスタンスログイン後
```
[ec2-user@ip-172-31-35-186 ~]$ ifconfig -a
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 9001
        inet 172.31.35.186  netmask 255.255.240.0  broadcast 172.31.47.255
        inet6 fe80::4b6:ddff:fe8b:7efc  prefixlen 64  scopeid 0x20<link>
        ether 06:b6:dd:8b:7e:fc  txqueuelen 1000  (Ethernet)
        RX packets 35345  bytes 51316820 (48.9 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 1365  bytes 117316 (114.5 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 8  bytes 648 (648.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 8  bytes 648 (648.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

おまけのコマンド
ユーザ情報の取得
```
aws sts get-caller-identity --profile cf-course
```

cliのユーザは有効なアカウントを使うこと
https://qiita.com/ryuzee/items/e3ce493f132f1981f57a

# EC2 User Data
EC2の起動時の実行シェルコマンドを設定できる


# EC2 Instance Launch Type
- On Demand : short workloads　従量課金制(コスト予想ができる)
- Reserved : 利用期間: 最低1年以上~3年まで
    - On-Demandより75%安い使用料金/時間
    - インスタンスタイプは固定
    - Reserved: long workloads 
    - Convertible: long workloads **with flexible instances**
        - EC2のタイプ変更可能
        - On-Demandより54%のディスカウント
    - Scheduled Reserved: 指定した期間のみ使用(ex:毎週木曜日のpm3時~pm6時だけ立ち上げる)
        - 日/週/月毎の起動時間の設定が可能
- Spot Instances: short workloads, 安価, インスタンスが自動で破棄される可能性がある, 信頼性:低
    - On Demandの90%ディスカウント
    -  スポットインスタンスとは、AWSサーバ上に存在し、使われていないEC2インスタンスに値段をつけ、その入札価格が現在のスポット価格(※長期ではなく、1回ごとの取引で決定され成立した市場価格)を上回っている限り、そのインスタンスを利用することができるというものです。スポットインスタンスでは、インスタンスが中断される可能性があるのでそれに備えておく必要があります。最高入札価格を高く設定することで、スポットインスタンスが終了する可能性を下げることができますが、中断を防ぐことはできません。**スポット価格が、設定した最高入札額を上回った場合は予期せずインスタンスが終了します**ので、停止を想定したインスタンスの利用を行なうことをおすすめします。
    - 技術的な検証に用いるのに向いてる
        - バッチ処理
        - データ解析
        - 画像処理
    - DBやクリティカルなジョブなど、長期的に安定運用する必要があるものには向かない
    - リザーブドインスタンスを土台に、ピーク時用にオンデマンドとスポットタイプを組み合わせるのが良いとされる
- Dedicated Host: 物理的なサーバーを予約可能,インスタンスの起動場所が指定できる
    - 物理的な個人専用EC2サーバ
    - EC2の起動場所が任意に指定できる
    - 3年間の使用予約ができる
    - 高い
    - Userful for software(BYOL-bring your own license)
- Dedicated Instance: 他の顧客が自分のハードウェアを共有しない(独占)
    - 自分専用に予約されたハードウェアで起動するインスタンス
    - ハードウェアを同じアカウント内の他のインスタンスとシェアすることもある
    - インスタンスの場所を移動することはできない
### まとめると(ホテルに例えると)
- On Demand: 好きな時にホテルに泊まり、泊まった分だけ料金を払う
- Reserved: 長期滞在するプランで、ディスカウントを受けられる
- Spot instaces: 空き部屋があればその時の最高入札額で
購入できると使用できる。より高い入札があれば部屋を追い出される
- Dedicatede Host: リゾートホテルそのものを予約する

## EC2 Spot Instance Request
- 90% off
- 入札可能額が時価総額を下回った場合、2分間の間にインスタンスを停止または終了することを選ぶ
- Spot Block: 一定の時間(1~6時間)、他のユーザの入札でインスタンスの使用権が奪われることを防ぐ
### Spot Fleets
- Spot Instance + (optional)On-Demand Instances

# EC2 Instance Types
- R :大容量RAMが必要なアプリ向け -in-memory caches
- C :高性能CPUが必要なアプリ向け -compute/database
- M :バランス型のアプリ向け(medium) -general/ web app
- I: 高性能ローカルI/O(instance storage)向け -database
> I/Oとは、Input/Outputの略で「入出力」を意味しています。
コンピューターは情報を入力（input）し、計算結果を出力（output）するという処理を行なっていますが、これらの処理を総称してI/Oと呼んでいます。
頻繁にデータの入出力が繰り返されることにより、ハードウェアやネットワークにI/O処理の負荷が発生することがあります。ハードディスクなどの記憶媒体に対するデータの読み書きについては「ディスクI/O」、ネットワークに対する入出力については「ネットワークI/O」と、I/O負荷の発生場所によって区別して表記することがあります。
大容量のデータの送受信にあたっては、リソースのI/O性能の高さが求められることになります。

- G: 高性能GPUが必要なアプリ向け -video rendering/machine learning
- T2/T3: burstable instances(up to capacity)
バースト可能タイプとは: 
CPUの使用上限をあげる必要があるケースで、クレジットというポイントを消費して、一定時間CPUの上限を上げてくれる。
- T2/T3 Unlimited: クレジット無制限のバースト可能型インスタンス

https://qiita.com/enshi/items/98a21728c4ce6f7a276c
>CPU100%の状態を1分間続けると1クレジット失われる（全インスタンスタイプ共通）
（例えば20%の状態を1分間続けば1/5クレジット、2分間続くと2/5クレジットが失われる）
逆に起動している間、常にクレジットは回復していく
インスタンスによって1時間に回復するクレジットが異なる（t2.nanoの場合1時間に3クレジット回復）
払うCPUがなくなったときは「ベースラインパフォーマンス（図で説明）」までのCPUしか使うことができなくなる。


>「CPU クレジット」。この言葉を聞いたことはございますでしょうか。例えば、Amazon EC2でT系インスタンスを使用しようとした場合、他のインスタンスタイプとは違い、ベースラインと呼ばれるあらかじめ決められたCPU使用率が定義されています。その上で、ベースラインを超えたCPU使用率が使用できる状況をバースト機能と呼びます。そのバースト機能を使用している時に使われるのが、CPUクレジットです。

もし、Amazon EC2でT系インスタンスを利用されていて、AWS Management Consoleにアクセスできる方は、そのインスタンスの「モニタリング」の欄をご覧ください。

## AMI
- Amazon Marketplaceで他の人が作成したAMIを使用することができる
- その場合、他の人のAMI使用量を自分が支払う。
- マルウェアなどの危険なAMIも存在するため、信頼できないAMIは使用しないこと
- AMIのデータはS3上に保存されている。
- AMIは他の他のAWSアカウントでシェアすることができる。
- AMIをシェアすることはAMIの所有権に影響しない。
- AMIを他のアカウントにコピーした場合、そのコピーの所有権はそのアカウント内のユーザになる。
- オリジナルのAMI所有アカウントは、他のアカウントに対して、AMIとそれに付随するEBSやS3に対するread permissionを付与する必要がある。
- コンソールからAMIをシェアするアカウントIDを設定することができる。

## Placement Groups
docs: https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/placement-groups.html

classmethod: https://dev.classmethod.jp/articles/ec2-placement-group-strategy/
-  Cluster: 単一AZ内の同一ハードウェア(Rack)内に作成する。
![placement-group-cluster](https://user-images.githubusercontent.com/54907440/101283259-98479580-381c-11eb-8ec7-ebd6d44c625f.png)
pros: インスタンス間の10Gpsのネットワーク帯域幅（高速通信）
cons: ラックが落ちると全てのインスタンスが落ちる(同一AZ内、同一Rack内のため), t2タイプにはclusterタイプのstrategyは設定できない
use case: ビッグデータ処理、ローレイテンシかつ高スループットを要求するアプリ

- Spread: 基盤ハードウェアを横断して1AZあたり
最大7個のインスタンスを分散する。
１つのインスタンスごとにハードウェアを割り当てることができる。
![placement-group-spread](https://user-images.githubusercontent.com/54907440/101283263-9b428600-381c-11eb-952a-5928b88e6fe7.png)

pros: マルチAZ展開できる、リスク分散。物理ハードウェアをインスタンスごとに分けることができる。
cons: １AZあたり最大7インスタンスまでの上限
use case: インスタンスごとを独立させ、インスタンスの停止を相互作用させないようにする必要があるアプリケーション(一つ落ちても他は生きている)、高いアベイラビリティーを要求するアプリケーション
 
-  Partition
![placement-group-partition](https://user-images.githubusercontent.com/54907440/101283262-9978c280-381c-11eb-951c-6cee05737a09.png)
-  一つのAZ内に複数のラック(パーティション)をもつ。
    - パーティションはそれぞれ独立している。
    - 上限:7パーティション/AZ
    - 最大: 100インスタンス/AZ
    - 同一ラック内のインスタンスに一つのインスタンスステータス異常が影響するが、他のパーティションには影響しない
    - メタデータとして、EC2はパーティションにアクセスする
use case: HDFS, HBase, Cassandra, Kafka

## ENI(Elastic Network Interfaces)
https://qiita.com/TaigoKuriyama/items/a1fcbadd10fa7ba41a04
> VPC上で実現する仮想ネットワークインタフェースで、物理的な環境におけるNIC（Network Interface Card）のこと。NICの場合は、サーバーに複数枚挿すことで、サーバーが担う複数の役割に応じてIPアドレスを複数持たせたり、異なるセグメント間で1台のサーバーを動作させたりすることができる。

classmethod:
https://dev.classmethod.jp/articles/vpc-ec2-multi-eip/

- ENIは以下の属性を持つ
    -  Primary private IPv4　+ secondary IPv4(1つまたは複数
    ) =>最低一つのプライマリプライベートIPをもつ
    -  1Elastic IP (IPv4)/private IPv4
    -  1Public IPv4
    -  1つ以上のsecurity groups
    -  A MAC address
- ENIを独立して作成し、EC2にアタッチしたり、他のEC2にすでにアタッチしたENIを移動させることができる。
- 特定のAZに属する。(AZは跨がない)

# EC2 Hibernate
- EC2の停止は以下の二つ。
Stop: 再起動までディスク(EBS)上のデータが補完される
Terminate: 破棄設定されているEBSボリューム(root)が消去される。
- 起動時には以下のことが起こる。
First Start: OS起動、UserDateスクリプトが実行される
Following Start: OSが起動する
=>　アプリケーションの起動、キャッシュのウォームアップが起こり時間がかかる。
```
インスタンスを休止すると、Amazon EC2 によってオペレーティングシステムに休止の実行 (suspend-to-disk) が指示されます。休止により、インスタンスメモリ (RAM) の内容が Amazon Elastic Block Store (Amazon EBS) ルートボリュームに保存されます。Amazon EC2 は、インスタンスのルートボリュームおよびアタッチされた EBS データボリュームを保持します。インスタンスを再起動すると、以下のようになります。
- EBS ルートボリュームは前の状態に復元されます。
- RAM の内容が再ロードされます。
- インスタンスで以前に実行されていたプロセスが再開されます。
- 以前にアタッチされていたデータボリュームが再アタッチされ、インスタンスがそのインスタンス ID を保持します。
```
## Hibernate(休止状態)
- インメモリの状態(RAM)が保存される
- インスタンスの起動が高速(OSは停止せず、再起動もしない)
https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/Hibernate.html

![hibernation-flow](https://user-images.githubusercontent.com/54907440/101356903-13bc4c00-38dc-11eb-8b4f-29a184414b36.png)



>インメモリデータベースはデータストレージを主にメインメモリ上で行うデータベース管理システムである。ディスクストレージ機構によるデータベースシステムと対比される。メインメモリデータベースは内部最適化アルゴリズムが簡素であり、相対的に少ないCPU命令を実行するので、ディスク最適化されたデータベースと比較して高速である




### ※terms
- **underlying hardware** 基盤ハードウェア
- **across** 横断して、またがって
- **bandwith** 帯域幅。ネットワーク帯域幅、データ帯域幅、またはデジタル帯域幅に分類される。
- **througput** 単位時間当たりの処理能力のこと。特に コンピュータやネットワークが一定時間内に処理できるデータ量のこと。レイテンシとならんで、パフォーマンスの評価基準となる。
- **logical componen**
- **fail over**
フェイルオーバーは、現用系コンピュータサーバ/システム/ネットワークで異常事態が発生したとき、自動的に冗長な待機系コンピュータサーバ/システム/ネットワークに切り換える機能を意味する。これに対して、何らかの異常を察知して、人間が手動で切り替えを行うことをスイッチオーバーという。

# メモ
- EC2は**停止中、または終了(削除)**されていると**課金されない。**
- **EIPがアタッチされている場合、**EIPはEC2が停止していても**EIPのみ課金される**。
- EIPは**EC2が起動している場合**はEIPの使用料金は**課金されない。**

EC2まとめ
https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c02/learn/lecture/13528082#overview