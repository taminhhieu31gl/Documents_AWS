Menu
=============
* [docker](#): Lab document
* [Deploy](#DEPLOY): Deploy
* [ECR](#ECR) 
* [Parameters Store](#Parameters_Store)
* [KMS](#KMS) 
* [ECS](#ECS) 
* [EKS](#EKS) 
* [EBS](#EBS) 
* [AWS COGNITO](#AWS_COGNITO) 
* [API GATEWAY](#API_GATEWAY) 
* [ELASTICACHE](#ELASTICACHE)
* [ROUTE 53](#ROUTE_53) 
* [AWS LAMBDA](#AWS_LAMBDA)
* [SQS](#SQS)
* [SNS](#SNS)
* [KINESIS](#KINESIS)
* [DYNAMO_DB](#DYNAMO_DB)
* [RDS](#RDS)
* [AWS CODEBUILD](#AWS_CODEBUILD)
* [AWS CODEDEPLOY](#AWS_CODEDEPLOY)


## DEPLOY

## ECR
StepByStep to deploy images to ECR:
1. build images
```bash
docker build -t hieutestecs:v1 .
```
2. tạo repository trên ECR
```bash
aws ecr create-repository --repository-name hieutademoecs --region ap-southeast-1
```
3. login vô ECR
```bash
aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin 148922931563.dkr.ecr.ap-southeast-1.amazonaws.com
```
4. commit images
```bash
docker tag hieutestecs:v1 148922931563.dkr.ecr.ap-southeast-1.amazonaws.com/hieutademoecs
```
5. push images
```bash
docker push 148922931563.dkr.ecr.ap-southeast-1.amazonaws.com/hieutademoecs
```

## Parameters_Store
aws ssm get-parameters --name Test
- Đặt tên parameter theo path: /my-app/prod/user && /my-app/prod/pass
```bash
aws ssm get-parameters-by-path --path /my-app/prod => ra list parameters có path...
aws ssm get-parameters-by-path --path /my-app/ --recursive => getAll()
```
- Với type = SecureString
```bash
aws ssm get-parameters --name Test --with-decryption =>hiển thị ra password value "hiểu được"
```
## KMS
1. Khi cần encrypted data trước khi gửi đến S3 => Use client-side encryption with a KMS managed CMK and SSL

## ECS
https://aws.amazon.com/vi/premiumsupport/knowledge-center/create-alb-auto-register/
=> Config network -> Create EC2 -> Create Target Group -> Create LoadBalancer với 2 EC2 -> Tạo Task trong ECS sử dụng LoadBalancer -> Tạo Cluster -> Tạo service -> gắn lb nảy tạo vào
1. Nhiều containers cần share data => tạo 1 task definition rồi **Mount a shared volume between those N containers** (ECS tasks support Docker volumes Docker Volume)
https://docs.aws.amazon.com/AmazonECS/latest/developerguide/docker-volumes.html
https://digitalcloud.training/certification-training/aws-developer-associate/aws-compute/amazon-ecs/

## EKS

## EBS

AWS Elastic Beanstalk provides several options for how deployments are processed, including deployment policies and options that let you configure batch size and health check behavior during deployments.
**All at once**: deploy 1 phát cho tất cả

· Deploys the new version to all instances simultaneously.

**Rolling**: 

· Update a few instances at a time (bucket), and then move onto the next bucket once the first bucket is healthy (downtime for 1 bucket at a time).

**Rolling with additional batch**:

· Like Rolling but launches new instances in a batch ensuring that there is full availability.

**Immutable**:

· Launches new instances in a new ASG and deploys the version update to these instances before swapping traffic to these instances once healthy.

· Zero downtime.

**Blue / Green deployment**:

· Zero downtime and release facility.

· Create a new “stage” environment and deploy updates there.

**EXAMPLE**
Khi cần deploy version mới nhưng vẫn muốn giữ version hiện tại hoạt động ổn định=> **Rolling with additional batch**

## AWS_COGNITO
1. Khi liên quan đến bên thứ ba (google, facebook,..) hoặc JWT
[![COGNITO](COGNITO "COGNITO")](https://mk0learndigital3jsdb.kinstacdn.com/wp-content/uploads/2020/04/Amazon-Cognito-User-Pool-Token-600x496.jpg "COGNITO")

https://digitalcloud.training/certification-training/aws-developer-associate/aws-security-identity-and-compliance/aws-cognito/

## API_GATEWAY
1. Cơ chế catche
https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-caching.html

## ELASTICACHE
1. Web Stateless => Key/Value stores for storing session state date: [DYNAMO_DB](#DYNAMO_DB), [ELASTICACHE](#ELASTICACHE) Redis
=> **ElastiCache will provide the lowest latency as it is an in-memory database.**



## ROUTE_53
1. Khi deploy 1 website ở nhiều regions => sử dụng AWS ROUTE 53 và dùng latency-based routing policy

## AWS_LAMBDA
1. AWS Lambda is a stateless compute service => cannot store session data in AWS Lambda itself
2. Lambda provides event source mappings for the following services (reads events): [KINESIS](#KINESIS), [DYNAMO_DB](#DYNAMO_DB), [SQS](#SQS)


## SQS
1. Khi Queue xử lý messages không nổi (vì nhiều mess) => Use the **ReceiveMessage** API to retrieve up to 10 messages at a time
2. Different Short Polling && Long Polling && Web Socket
https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-short-and-long-polling.html
4. Để giảm chi phí SQS -> chuyển sang impl Long Polling bằng cách **Set the ```bash ReceiveMessage``` API with a WaitTimeSeconds of 20**
https://digitalcloud.training/certification-training/aws-developer-associate/aws-application-integration/amazon-sqs/

## SNS
1. Cần bắn events khi uploaded gì đó lên S3 => **add an event S3 -> use SNS Topic**


## KINESIS

## DYNAMO_DB
1. Cơ chế Stateless
2. Different Scan && Query?
3. Different Items && Collections?
4. DynamoDB Stream?
5. Is DynamoDB query cheaper than scan? => Yes, why?
6. What is the DynamoDB scan vs query performance difference?
If you need to access data identified by known keys, query is much faster because of the direct access method.
7. I need to use scan. Can I make it faster? => **using DynamoDB Parallel Scan**
8. When scan periodically(định kỳ) => **Set a smaller page size for the scan**

https://dynobase.dev/dynamodb-scan-vs-query

## RDS

## AWS_CODEBUILD
1. Khi bị error stated that the length of all environment variables exceeds the limit for the combined maximum of characters
=> dùng (#Parameters_Store) can store an individual environment variable (name and value added together) that is a combined 4,096 characters or less.

## AWS_CODEDEPLOY
1. appspec.yaml structure = **BeforeAllowTraffic > AfterAllowTraffic**
