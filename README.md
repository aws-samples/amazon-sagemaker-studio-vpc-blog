## SageMaker Studio VPC Mode

With the VPC Mode enabled on SageMaker Studio, all traffic from the SageMaker instances flow through the customer selected VPC.
This CloudFormation template deploys an infrastructure ready to test this functionality, containing:

1. sm-studio-vpc-infra.template.yaml: Main template whihc launches a set of nested templates to provision the products in their entirety
2. vpc_template.yaml: Creates a secure project VPC without internet connectivity and a private subnet. Also creates the VPC Endpoints to allow Studio to securely connect to other AWS Services as S3
3. iam_template.yaml: Provisions the IAM Execution Role to be used by SageMaker Studio
4. s3_template.yaml: Creates a sample S3 buckets that will be used to demonstrate the secure connectivity

Once the environment is ready, you can create the SageMaker Studio Domain with the following CLI command.
The EXECUTION_ROLE_NAME and SECURITY_GROUP can be found on the Output section of the CloudFormation template above.

```
#Fill out below params first
REGION=
AWS_ACCOUNT_ID=
EXECUTION_ROLE_NAME=
VPC_ID=
#Provide the private subnet Ids here
SUBNET_IDS=
SECURITY_GROUP=

aws --region $REGION sagemaker create-domain --domain-name "vpc-domain" --vpc-id $VPC_ID --subnet-ids $SUBNET_IDS --app-network-access-type VpcOnly --auth-mode IAM --default-user-settings "ExecutionRole=arn:aws:iam::${AWS_ACCOUNT_ID}:role/${EXECUTION_ROLE_NAME},SecurityGroups=${SECURITY_GROUP}"
```

To create a Studio user profile:

```
#Fill out Domain Id from last step
#You can change user-profile-name to create more users under same domain
DOMAIN_ID=
aws --region $REGION sagemaker create-user-profile --domain-id $DOMAIN_ID --user-profile-name user1
```

To access Studio:

Create a pre-signed URL

```
#Fill out Domain Id and User Profile Name from last step
DOMAIN_ID=
USER_PROFILE_NAME="user1"
aws --region $REGION sagemaker create-presigned-domain-url --domain-id $DOMAIN_ID --user-profile-name $USER_PROFILE_NAME
```

> Since Studio is now connected to the VPC, you require access to this VPC to access Studio

In your web browser, visit the presigned URL generated from create-presigned-domain-url above

## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.

