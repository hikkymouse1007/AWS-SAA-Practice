ec2-apache:
	aws cloudformation deploy \
    --template-file ./ec2-apache.yaml \
    --stack-name ec2-apache \
    --parameter-overrides \
    KeyName=udemy \

ec2-apache-dlt:
	aws cloudformation delete-stack \
    --stack-name ec2-apache \

ec2-info:
	aws ec2 describe-instances --query "Reservations[].Instances[].InstanceId"

ec2-info-table:
	aws ec2 describe-instances --output=table

ec2-info-active:
	aws ec2 describe-instances  --query "Reservations[].Instances[].InstanceId"

cli-user:
	aws sts get-caller-identity --profile cf-course
