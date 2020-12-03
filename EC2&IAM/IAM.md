# IAM(Identity and Access Management)
## ３要素からなる
Policy(以下を定義する)
   ├── Users
   ├── Groups
   └── Roles

- Users
物理的なユーザ
- Groups
    - Functions(admins, devops)
    - Teams(engineering, design)
- Role
    - AWSのリソース使用権限


##　メモ
- Rootは不使用推奨・シェア厳禁
- Userに適切な権限を与える(不必要に権限を与えないこと)
- PolicyはJSON
- IAM Roleは1ロール/1アプリ
- IAM credentialは共有しないこと
