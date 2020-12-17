## EBS(Elastic Block Storage)
- EC2は削除される際にrootボリュームを失う
- 稀に不慮の削除が起こる
- そのためデータをどこかへ保存する必要がある
=> EBS Volumeは稼働中のインスタンスへアタッチできるネットワークドライブである(物理ドライブではない) = USBのようなもの

### 特徴
1. ネットワークドライブである
    - インスタンスへの接続にネットワークを使っているのでレイテンシーが存在する
    - インスタンスからデタッチし、他のインスタンスにアタッチすることができる。

2. AZは固定される
    - EBSボリュームのeast-1aからeast-1bへのアタッチはできない
    - ボリュームをAZを跨がせたい時はスナップショットを取る

3. プロビジョンドキャパシティ(GBsサイズ, IOPS)
    - 全てのプロビジョンドキャパシティが課金対象
    - ドライブの容量はいつでも増加可能

>プロビジョンドIOPS(＝PIOPS)について、まず単語を和訳してみましょう。プロビジョン＝Provisionとは「支給・貯蔵」という意味があります。AWSにおける「支給・貯蔵」の意味は「AWSが利用者のために「何か」の支給量を約束しますよ」ということを意味します。この「何か」がIOPSにあたるのですが、この「IOPS」は比較的一般的なIT用語で「Input Output Per Second」の略称です。「1秒間にどのぐらいのインプット(入力)やアウトプット(出力)ができるのか」ということを意味します。上記二つを合わせると「プロビジョンドIOPS」とは、「1秒間にどのぐらいの入力や出力ができるのかという支給量」が「AWSから約束される」という事を意味します。つまりプロビジョンドIOPSの付加機能をONにすることで、例えば仮想データベースがストレージとの間でデータを入出力する際のスピードをAWSから保障してもらうことができるのです。これによって仮想データベースの平均的なデータ入出力速度が向上することになるのです。プロビジョンドIOPSの付加機能をOFFにしている場合、仮想データベースとストレージとのデータ入出力速度はAWS全体の混み具合によって低下することがあります。このような「スピードを保障しない」状態でのサービス提供形態を「ベストエフォート(＝最大限の努力)」と呼びます。IOPSにおいては「プロビジョンドIOPS」と「ベストエフォートIOPS」は相反する状態として用いられる言葉です。

### EBS Volume Types
4つのタイプが存在する
- GP2(SSD) 価格とパフォーマンスのバランス型
- IOI(アイオーワン)(SSD) 特定の目的に特化した最高パフォーマンス型 e.g)低レイテンシー, 高スループット負荷, DBに使用される
- STI(HDD) 低コストかつ高負荷アクセス向け
- SCI(HDD) 最安HDDであり、低負荷アクセス向け
**GP2とIOIのみbootボリュームが使用可能**

AWS SAブログ
https://aws.typepad.com/sajp/2014/04/trainingfaqbest10.html

http://endok.hatenablog.com/entry/2014/06/15/193137
>AMI = EC2インスタンスの元となるテンプレート
EBS = インスタンス作成時にAMIから情報がコピーされる場所
というのが正しかったようだ。
つまりAMIが起動されるという考え方が間違っていて、
インスタンス作成時にAMIのテンプレート内容がEBSにコピー
↓
その後はEBSからインスタンスが起動される
といった形になるらしい。

### Storage
USBのようなイメージ
![ebs](https://user-images.githubusercontent.com/54907440/102502445-f3ea0c80-40c1-11eb-8b6e-73d3232c99d2.png)
最初にインスタンスの設定画面のストレージで登録したEBSは
インスタンスにアタッチされている
![スクリーンショット 2020-12-17 23 18 30](https://user-images.githubusercontent.com/54907440/102502457-f5b3d000-40c1-11eb-9529-d094caac5ebd.png)
![スクリーンショット 2020-12-17 23 18 42](https://user-images.githubusercontent.com/54907440/102502461-f6e4fd00-40c1-11eb-8cc9-603b09331aa9.png)

LinuxでEBSを使用可能にする方法
https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/ebs-using-volumes.html

インスタンス内にストレージを確認
xvdaはルートディレクトリににアタッチされている。
xvdbはまだのディレクトリにもアタッチされていないので、
そのままではアクセスできない

```
[ec2-user@ip-172-31-7-207 ~]$ lsblk
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
xvda    202:0    0   8G  0 disk
└─xvda1 202:1    0   8G  0 part /
xvdb    202:16   0   2G  0 disk
```

>ボリュームにファイルシステムがあるかどうかを確認します。新しいボリュームは未加工のブロックデバイスであるため、マウントして使用する前に、ボリュームにファイルシステムを作成する必要があります。スナップショットから作成されたボリュームは、多くの場合既にファイルシステムがあります。既存のファイルシステムの上に新しいファイルシステムを作成すると、データが上書きされます。

デバイスに関する情報 (ファイルシステムの種類など) を一覧表示するには、**file -s** コマンドを使用します。次の出力例のように、出力に data だけが表示されている場合は、デバイスにはファイルシステムが存在していないため、ファイルシステムを作成する必要があります。


```
[ec2-user@ip-172-31-7-207 ~]$ sudo file -s /dev/xvdb
/dev/xvdb: data
```

空のボリュームがある場合は、mkfs -t コマンドを使ってボリューム上にファイルシステムを作成します。

```
[ec2-user@ip-172-31-7-207 ~]$sudo mkfs -t xfs /dev/xvdb
meta-data=/dev/xvdb              isize=512    agcount=4, agsize=131072 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=0
data     =                       bsize=4096   blocks=524288, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0

// ファイルシステムが作成された
[ec2-user@ip-172-31-7-207 ~]$ sudo file -s /dev/xvdb
/dev/xvdb: SGI XFS filesystem data (blksz 4096, inosz 512, v2 dirs)
```

- ディレクトリマウント
>mkdir コマンドを使用して、ボリュームのマウントポイントディレクトリを作成します。マウントポイントとは、ボリュームをマウントした後、ファイルシステムツリー内でボリュームが配置され、ファイルの読み書きが実行される場所です。次の例では、/data という名前のディレクトリが作成されます。

```
// /dataディレクトリにボリュームをマウント
[ec2-user@ip-172-31-7-207 ~]$ sudo mkdir /data
[ec2-user@ip-172-31-7-207 ~]$ sudo mount /dev/xvdb /data
[ec2-user@ip-172-31-7-207 ~]$ cd /data

// dataディレクトリにファイルを作成
[ec2-user@ip-172-31-7-207 data]$  sudo touch hello.txt
[ec2-user@ip-172-31-7-207 data]$ sudo vim hello.txt
[ec2-user@ip-172-31-7-207 data]$ cat hello.txt
hello world

// xvdbが/dataにマウントされていることを確認
[ec2-user@ip-172-31-7-207 data]$ lsblk
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
xvda    202:0    0   8G  0 disk
└─xvda1 202:1    0   8G  0 part /
xvdb    202:16   0   2G  0 disk /data
```

## 再起動後に接続ボリュームを自動的にマウントする
>システムブート時に常に、このアタッチ済みの EBS ボリュームをマウントするには、/etc/fstab ファイルにデバイス用のエントリを追加します。

```
//　fstabのバックアップ作成
[ec2-user@ip-172-31-7-207 data]$  sudo cp /etc/fstab /etc/fstab.orig

//　指定したマウントポイントへデバイスをマウント
[ec2-user@ip-172-31-7-207 data]$ sudo blkid
/dev/xvda1: LABEL="/" UUID="15c7809d-e6e3-4062-a5eb-afeb1939fc6e" TYPE="xfs" PARTLABEL="Linux" PARTUUID="cfb2e895-ecec-41c5-afc2-40ffe2e4384a"
/dev/xvdb: UUID="4438fb41-dc3b-45c5-9b58-106617b8470e" TYPE="xfs"

// 上のUUIDを追加
sudo vim /etc/fstab
#
UUID=15c7809d-e6e3-4062-a5eb-afeb1939fc6e     /           xfs    defaults,noatime  1   1
UUID=4438fb41-dc3b-45c5-9b58-106617b8470e /data  xfs  defaults,nofail  0  2

// アンマウント・マウントを実行して、上での入力が正しいことを確認
[ec2-user@ip-172-31-7-207 /]$ sudo umount /data
[ec2-user@ip-172-31-7-207 /]$ lsblk
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
xvda    202:0    0   8G  0 disk
└─xvda1 202:1    0   8G  0 part /
xvdb    202:16   0   2G  0 disk
[ec2-user@ip-172-31-7-207 /]$sudo mount -a
[ec2-user@ip-172-31-7-207 /]$ lsblk
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
xvda    202:0    0   8G  0 disk
└─xvda1 202:1    0   8G  0 part /
xvdb    202:16   0   2G  0 disk /data
```
## Snapshot
- インクリメンタル
- アプリが稼働中かつアクセスが多いときはsnapshotを取るべきでない
- SnapshotはS3に保存される
- スナップショットを取る時はボリュームのデタッチは必須ではないが推奨
- 最大100,000スナップショット保存可能
- AZをまたがってコピー可能
