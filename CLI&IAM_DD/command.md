## EC2のCLIのベストプラクティス
- IAM RolesはEC2インスタンスにアタッチ可能
- IAM RolesにはEC2に許可するアクションを詳細に設定できる
- EC2インスタンスにIAMRoleを設定するとその設定は自動的に適用される。

## S3

```
// バケットの作成
aws s3 mb s3://hoegaphgprjg

// 削除
aws s3 rb s3://hoegaphgprjg

// バケットのリスト
aws s3 ls

// バケットの中身
aws s3 ls s3://mashimo-cloudformation-stack

// バケットからのダウンロード
aws s3 cp s3://mashimo-cloudformation-stack/ssh-sg.yaml ssh-sg_cp.yaml
```