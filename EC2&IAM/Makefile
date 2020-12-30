ec2-apache:
	aws cloudformation deploy \
    --template-file ./Yaml/ec2-apache.yaml \
    --stack-name ec2-apache \
    --parameter-overrides \
    KeyName=udemy \

ec2-apache-dlt:
	aws cloudformation delete-stack \
    --stack-name ec2-apache \

ec2-info:
	aws ec2 describe-instances 

ec2-info-table:
	aws ec2 describe-instances --output=table

ec2-status:
	aws ec2 describe-instance-status \
    --instance-ids i-0f440a3d2a115fded


ec2-info-ids:
	aws ec2 describe-instances  --query "Reservations[].Instances[].InstanceId"

cli-user:
	aws sts get-caller-identity --profile cf-course
