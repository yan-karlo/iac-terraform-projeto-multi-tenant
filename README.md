# Scenario #
This terraformation infrastructure is simulating a multi-tenant environment for a company called HumanGov, and requisites are:
1. Not share any resource
2. Each province is supposed to have its own AWS infrastructure
3. The terraformation state file and its lock are supposed to handled in the cloud, to avoid overwriting.

# Assumptions #
##### 1. You already have an AWS account. ####
##### 2. You must be logged in your AWS account through AWS cli. ####
##### 3. You have permissions to create a S3 Bucket and a DynamoDB table. #### ####

# How ro run it #
**Please, make sure that terminal prompt folder is the project&apos;s root folder, then run the command below.**

------------

##### 1. Creating a AWS bucket to store the terraform.tfstate file #####
```
aws s3api create-bucket --bucket humangov-terraform-state-ykbt --region us-east-1
```


##### 2. Creating a DynamoDB table to acts as the lock #####
   It is to avoid more than one ``terraform apply`` running at the same moment.

```
aws dynamodb create-table \
--table-name tcb-devops-state-lock-table \
--attribute-definitions AttributeName=LockID,AttributeType=S \
--key-schema AttributeName=LockID,KeyType=HASH \
--provisioned-throughput ReadCapacityUnits=5,WriteCapacityUnits=5 \
--region us-east-1
```

##### 3. Initiating the terraform environment #####
The commands below create ``.terraform`` folder in the project&apos;s terraform folder, downloading all providers binary files besides to set in a aws bucket as common place to store terraform.state file, and defining a DynamoDB table as the locker for sure that the team is sharing the same terraform state. During this process a warning question is going to made, asking whether you&apos;re aware that the when initiating the terraform.tfstate will placed into the defined S3 bucket, so just type "yes".
```shell
cd terraform && terraform init
```

##### 4. Defining terraformation log file (optional) ##### #####
- This set will make the screen output more verbose.
```shell
export TF_LOG = true
```
- This set will dump all screen messages into a log file, which the name can be whatever you want.`
```shell
export TF_LOG_PATH = terraform.log
```

##### 4. Validating the infrastructure&apos;s syntax (optional) #####
Just checking if the code is written correctly.
`terraform validate`

##### 5. Checking the infrastructure planning (optional)#####
Just checking if the whole infrastructure is going to be created.
```shell
terraform plan -out=terraform_plan
```
or
```shell
terraform plan
```

##### 6. Applying the Infrastructure #####
Applies the infrastructure as planned.
```shell
terraform apply
```
or
```shell
terraform apply -auto-approve
```
or
```shell
terraform apply terraform_plan
```