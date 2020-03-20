# ProxySQL
1. The command to run build-template to initialize ec2 with proxysql 
```bash
aws cloudformation create-stack --region us-west-2 \
  --stack-name proxysql-$(date +%d%H%M%S) \
  --template-body file://ec2-proxysql-build.yaml \
  --parameters \
    ParameterKey="KeyName",ParameterValue="KEY NAME" \
    ParameterKey="PubKey",ParameterValue="PUBLIC KEY" \
    ParameterKey="SGIngressSSH",ParameterValue="SECURITY GROUP ID" \
    ParameterKey="OSUser",ParameterValue="OS USER NAME" \
    ParameterKey="VpcId",ParameterValue="VPC ID" \
    ParameterKey="SubnetId",ParameterValue="SUBNET ID"
```
2. Getting id of EC2 Inatance which made by previous step
```bash
> aws cloudformation describe-stack-resource --region us-west-2 \
--stack-name CLOUD STACK NAME \
--logical-resource-id EC2Instance

Output might be:
{
    "StackResourceDetail": {
        "StackId": "arn:aws:cloudformation:us-west-2:123456789101:stack/STACK NAME/ee", 
        "ResourceStatus": "CREATE_COMPLETE", 
        "DriftInformation": {
            "StackResourceDriftStatus": "NOT_CHECKED"
        }, 
        "ResourceType": "AWS::EC2::Instance", 
        "LastUpdatedTimestamp": "2020-03-20T05:16:46.560Z", 
        "StackName": "STACK NAME", 
        "PhysicalResourceId": "i-0abcdabcd12345678", 
        "Metadata": "{}", 
        "LogicalResourceId": "EC2Instance"
    }
}

# Then create AMI
> aws ec2 create-image --region us-west-2 \
--no-dry-run --reboot \
--instance-id i-0abcdabcd12345678 --name proxysql

```
2. Getting id of EC2 Inatance which made by previous step
```bash
aws cloudformation create-stack --region us-west-2 \
  --stack-name proxysql-$(date +%d%H%M%S) \
  --template-body file://ec2-proxysql-deploy.yaml \
  --parameters \
    ParameterKey="SGIngressSSH",ParameterValue="SECURITY GROUP ID" \
    ParameterKey="AZ1SubnetId",ParameterValue="SUBNET ID" \
    ParameterKey="AZ2SubnetId",ParameterValue="SUBNET ID" \
    ParameterKey="VpcId",ParameterValue="VPC ID" \
    ParameterKey="AMI",ParameterValue="AMI ID"
```

The policy have to be granted for executing cloudformation
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "ec2:ReleaseAddress",
                "ec2:DisassociateAddress",
                "ec2:AuthorizeSecurityGroupEgress",
                "ec2:AuthorizeSecurityGroupIngress",
                "ec2:DescribeInstances",
                "ec2:DescribeAddresses",
                "ec2:TerminateInstances",
                "ec2:CreateImage",
                "ec2:RunInstances",
                "cloudformation:DescribeStackResource",
                "ssm:GetParameters",
                "ec2:DescribeSecurityGroups",
                "ec2:AllocateAddress",
                "ec2:DescribeImages",
                "cloudformation:CreateStack",
                "ec2:DescribeVpcs",
                "ec2:CreateSecurityGroup",
                "cloudformation:DeleteStack",
                "ec2:DeleteSecurityGroup",
                "ec2:DescribeSubnets",
                "ec2:DescribeKeyPairs",
                "ec2:AssociateAddress"
            ],
            "Resource": "*"
        }
    ]
}
```