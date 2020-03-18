# ProxySQL
The command to run cloudforatmion 
```bash
aws cloudformation create-stack --region us-west-2 \
  --stack-name proxysql-$(date +%d%H%M%S) \
  --template-body file://ec2-proxysql.yaml \
  --parameters \
    ParameterKey="KeyName",ParameterValue="KeyName" \
    ParameterKey="PubKey",ParameterValue="PubKey" \
    ParameterKey="SGIngressSSH",ParameterValue="SGID" \
    ParameterKey="VpcId",ParameterValue="VpcId" \
    ParameterKey="SubnetId",ParameterValue="SubnetId"
```
The policy to grant for creating stack
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
                "ec2:RunInstances",
                "ssm:GetParameters",
                "ec2:DescribeSecurityGroups",
                "ec2:AllocateAddress",
                "ec2:DescribeImages",
                "cloudformation:CreateStack",
                "ec2:DescribeVpcs",
                "ec2:CreateSecurityGroup",
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