# EC2 MetaData
- MetaDataはEC2の情報
- IAMRoleを使うことなくEC2に自身の情報を学習させることができる。
- metadataからはIAMRoleの名前が取得可能だが、Policyは取得できない。
- 実行中のインスタンス内からインスタンスメタデータのすべてのカテゴリを表示するには、EC2の中から次のURIを使用する。
    http://169.254.169.254/latest/meta-data/  
    公式Doc: 
https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/instancedata-data-retrieval.html

- Metadata = EC2の情報
    
    Userdata = EC2の起動スクリプト
```
[ec2-user@ip-172-31-32-213 ~]$ curl http://169.254.169.254/latest/
dynamic
meta-data

[ec2-user@ip-172-31-32-213 ~]$curl http://169.254.169.254/latest/meta-data/
ami-id
ami-launch-index
ami-manifest-path
block-device-mapping/
events/
hostname
identity-credentials/
instance-action
instance-id
instance-life-cycle
instance-type
local-hostname
local-ipv4
mac
metrics/
network/
placement/
profile
public-hostname
public-ipv4
public-keys/
reservation-id
security-groups

[ec2-user@ip-172-31-32-213 ~]$ curl http://169.254.169.254/latest/meta-data/hostname
ip-172-31-32-213.ap-northeast-1.compute.internal
```