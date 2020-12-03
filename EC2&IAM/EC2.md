## SSH
key.pemのパーミッションは400にする
ssh ec2-user@54.199.50.230 -i ~/.ssh/key.pem

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


