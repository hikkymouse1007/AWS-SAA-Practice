## IAM policy
### ポリシーの作成
IAMのポリシーは自分で設定できる。
項目は以下の4つ
- サービス
ポリシーを設定するAWSのサービスを選択する(EC2, S3 etc...)
<img width="831" alt="スクリーンショット 2020-12-30 22 28 26" src="https://user-images.githubusercontent.com/54907440/103354440-e33c8c00-4aee-11eb-871e-bd5aeb1dfe1c.png">

- アクション
<img width="902" alt="スクリーンショット 2020-12-30 22 29 08" src="https://user-images.githubusercontent.com/54907440/103354442-e6377c80-4aee-11eb-9b84-9be460e758bd.png">


- リソース
例えばS3の場合は、アクセスを許可するバケット・オブジェクトを設定できる
<img width="1203" alt="スクリーンショット 2020-12-30 22 29 47" src="https://user-images.githubusercontent.com/54907440/103354444-e768a980-4aee-11eb-9bc5-1c7849e1fd1c.png">
<img width="592" alt="スクリーンショット 2020-12-30 22 30 10" src="https://user-images.githubusercontent.com/54907440/103354391-c1430980-4aee-11eb-9e03-fa68613c11e4.png">

- リクエスト条件
(オプション)
<img width="431" alt="スクリーンショット 2020-12-30 22 30 37" src="https://user-images.githubusercontent.com/54907440/103354399-c3a56380-4aee-11eb-9f5b-e25996b1d7c7.png">

### inline policy
既存のロールにのみアタッチできるポリシー。
他のロールにアタッチすることはできない。

### policyのアクセス権限
JSON形式で権限設定ができる。
<img width="804" alt="スクリーンショット 2020-12-30 22 21 06" src="https://user-images.githubusercontent.com/54907440/103353944-8be9ec00-4aed-11eb-9d29-8bf4e3041b74.png">

### AWS Policy Simulator
AWSのIAMのポリシー設定を確認することができる
<img width="1283" alt="スクリーンショット 2020-12-30 22 42 04" src="https://user-images.githubusercontent.com/54907440/103355033-8d68e380-4af0-11eb-9778-f9a8379acfcf.png">

https://policysim.aws.amazon.com/home/index.jsp?#



