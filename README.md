Menu
=============
* [docker](#): Lab document
* [ECR](#ECR) 
* [Parameters Store](#Parameters_Store)
* [KMS](#KMS)
* [EC2](#EC2) 
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
* [SES](#SES)
* [SWF](#SWF)
* [S3](#S3)
* [KINESIS](#KINESIS)
* [DYNAMO DB](#DYNAMO_DB)
* [RDS](#RDS)
* [AWS CODEBUILD](#AWS_CODEBUILD)
* [AWS CODEDEPLOY](#AWS_CODEDEPLOY)
* [CLOUD FRONT](#CLOUD_FRONT)
* [CLOUD FORMATION](#CLOUD_FORMATION)
* [CLOUD WATCH](#CLOUD_WATCH)
* [CLOUD TRAIL](#CLOUD_TRAIL)
* [IAM](#IAM)


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
2. 
![image](https://user-images.githubusercontent.com/42288000/139084062-012d0e09-fac7-4666-9a12-63b11a9a8984.png)

3. KMS does not store, manage or track data keys, you are responsible for this in your application.
## EC2

## ECS
https://aws.amazon.com/vi/premiumsupport/knowledge-center/create-alb-auto-register/
=> Config network -> Create EC2 -> Create Target Group -> Create LoadBalancer với 2 EC2 -> Tạo Task trong ECS sử dụng LoadBalancer -> Tạo Cluster -> Tạo service -> gắn lb nảy tạo vào
1. Nhiều containers cần share data => tạo 1 task definition rồi **Mount a shared volume between those N containers** (ECS tasks support Docker volumes Docker Volume)
https://docs.aws.amazon.com/AmazonECS/latest/developerguide/docker-volumes.html
https://digitalcloud.training/certification-training/aws-developer-associate/aws-compute/amazon-ecs/
2. Amazon ECS supports the following task placement strategies:
- binpack: giảm thiểu số phiên bản sử dụng
- random: đặt task ngẫu nhiên
- spread: đặt task đều dựa trên các giá trị sử dụng...

3. Để task definition không xung đột port => **chỉ định port cho container và port 0 cho host port**

Example:
[![ECS](
https://img-b.udemycdn.com/redactor/raw/2020-04-19_12-14-21-2e05425ee95cbc437d856fb9b53d6aa2.jpg?secure=1Ca7jDPh_rnWgpf_tr5aHQ%3D%3D%2C1618647458 "ECS")](
https://img-b.udemycdn.com/redactor/raw/2020-04-19_12-14-21-2e05425ee95cbc437d856fb9b53d6aa2.jpg?secure=1Ca7jDPh_rnWgpf_tr5aHQ%3D%3D%2C1618647458 "ECS")

https://digitalcloud.training/certification-training/aws-developer-associate/aws-compute/amazon-ecs/

## EKS

## EBS

AWS Elastic Beanstalk provides several options for how deployments are processed, including deployment policies and options that let you configure batch size and health check behavior during deployments.

- **All at once**: deploy 1 phát cho tất cả

· Deploys the new version to all instances simultaneously.

- **Rolling**: 

· Update a few instances at a time (bucket), and then move onto the next bucket once the first bucket is healthy (downtime for 1 bucket at a time).
![image](https://user-images.githubusercontent.com/42288000/141314822-9dae7548-e911-4d70-9974-93525832640d.png)


- **Rolling with additional batch**:

· Like Rolling but launches new instances in a batch ensuring that there is full availability.
![image](https://user-images.githubusercontent.com/42288000/140940494-f746171f-25ce-4a13-9835-862490ab0d4e.png)
![image](https://user-images.githubusercontent.com/42288000/142218833-9eb1f0d8-ceb4-4aa1-8df2-7c6b8affe6f5.png)


- **Immutable**:

· Launches new instances in a new ASG and deploys the version update to these instances before swapping traffic to these instances once healthy.

· Zero downtime.
![image](https://user-images.githubusercontent.com/42288000/140947520-ce4ef2d8-7a44-402f-b68d-81324b45373d.png)
![image](https://user-images.githubusercontent.com/42288000/141800412-c91f69a3-4d7a-4556-a381-5dc3c9402c0b.png)
![image](https://user-images.githubusercontent.com/42288000/142870005-48fd4ab4-300d-424a-a781-3cc14c0eaa30.png)


- **Blue / Green deployment**:

· Zero downtime and release facility.

· Create a new “stage” environment and deploy updates there.

Example:
Khi cần deploy version mới nhưng vẫn muốn giữ version hiện tại hoạt động ổn định=> **Rolling with additional batch**
Khi cần thiết cập nhật code nhanh chóng => **Blue/Green**

## AWS_COGNITO
1. Khi liên quan đến bên thứ ba (google, facebook,..) hoặc JWT
[![AWS_COGNITO](https://mk0learndigital3jsdb.kinstacdn.com/wp-content/uploads/2020/04/Amazon-Cognito-User-Pool-Token-600x496.jpg "AWS_COGNITO")](https://mk0learndigital3jsdb.kinstacdn.com/wp-content/uploads/2020/04/Amazon-Cognito-User-Pool-Token-600x496.jpg "AWS_COGNITO")
2. The two main components of AWS Cognito are user pools and identity pools:

**User pools** are user directories that provide sign-up and sign-in options for your **app users**.
**Identity pools** enable you to grant your users access to **other AWS services**.

https://digitalcloud.training/certification-training/aws-developer-associate/aws-security-identity-and-compliance/aws-cognito/

## API_GATEWAY
1. Cơ chế catche
https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-caching.html

## ELASTICACHE
1. Web Stateless => Key/Value stores for storing session state date: [DYNAMO_DB](#DYNAMO_DB) (chỉ chứa dc 400KB), [ELASTICACHE](#ELASTICACHE)(mắc tiền) Redis, (Optional: [S3](#S3) (không liên quan web stateless nhưng cũng chứa dc key/value))
=> **ElastiCache will provide the lowest latency as it is an in-memory database.**

2. Different Sticky sessions && Destributed Cache?
3. A Distributed cache is suitable for storing session state data => **Using [ELASTICACHE](#ELASTICACHE)**
4. **IMPORTANT:** fully managed implementation of two popular in-memory data stores – **Redis** && **Memcached**
5. Different **Redis** && **Memcached**?
<table>
 <tr>
  <th>MEMCACHED</th>
  <th>REDIS</th>
 </tr>
 <tr>
  <td>Simplest model and can run large nodes.</td>
  <td>Open-source in-memory key-value store.</td>
 </tr>
 <tr>
  <td>Can be scaled in and out and cache objects such as DBs.</td>
  <td>Supports more complex data structures: sorted sets and lists.</td>
 </tr>
 <tr>
  <td>Widely adopted memory object caching system.</td>
  <td>Supports master / slave replication and multi-AZ for cross-AZ redundancy.</td>
 </tr>
 <tr>
  <td>Multi-threaded.</td>
  <td>Support automatic failover and backup/restore.</td>
 </tr> 
</table>


## ROUTE_53
1. Khi deploy 1 website ở nhiều regions => sử dụng AWS ROUTE 53 và dùng latency-based routing policy
![image](https://user-images.githubusercontent.com/42288000/137514144-87553f32-509f-4ad2-af4d-2654e67039e0.png)

## AWS_LAMBDA
1. AWS Lambda is a stateless compute service => cannot store session data in AWS Lambda itself
2. Lambda provides event source mappings for the following services (reads events): [KINESIS](#KINESIS), [DYNAMO_DB](#DYNAMO_DB), [SQS](#SQS)
3. API Involke https://docs.aws.amazon.com/lambda/latest/dg/API_Invoke.html
https://digitalcloud.training/certification-training/aws-developer-associate/aws-compute/aws-lambda/
4. /tmp space => **(khi function cần để download a large file,max size > 512 MB)?S3 : /tmp)**


## SQS
1. Khi Queue xử lý messages không nổi (vì nhiều mess) => Use the **ReceiveMessage** API to retrieve up to 10 messages at a time
2. Different Short Polling && Long Polling && Web Socket
https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-short-and-long-polling.html
4. Để giảm chi phí SQS -> chuyển sang impl Long Polling bằng cách **Set the ```bash ReceiveMessage``` API with a WaitTimeSeconds of 20**
https://digitalcloud.training/certification-training/aws-developer-associate/aws-application-integration/amazon-sqs/
5. Dead Letter Queue (DLQ) cơ chế hàng đợi những mess không được gửi đi, bị "chết"....
=> Khi [AWS LAMBDA](#AWS_LAMBDA) function gọi [SNS](#SNS) lỗi liên tục => config DLQ trong [AWS LAMBDA](#AWS_LAMBDA) để xem log

## SNS
1. Cơ chế Pub/Sub? (Optional: phân biệt với cơ chế P2P trong JMS (java))
2. Cần bắn events khi uploaded gì đó lên S3 => **add an event S3 -> use SNS Topic**
3. Push notifications => mess, mail... => **SNS**
4. generate SMS text messages and emails for a large number of subscibers
https://digitalcloud.training/certification-training/aws-developer-associate/aws-application-integration/amazon-sns/

## SES
1. this service only sends email, not SMS text messages

## SWF
1. Simple Workflow Service is used for orchestrating multi-step workflows.

## S3
___Exam tip: The AWS Certified Developer Associate exam tests your understanding of S3 encryption heavily. Make sure you understand S3 encryption well before taking the exam.___
[![S3](https://mk0learndigital3jsdb.kinstacdn.com/wp-content/uploads/2019/03/Amazon-S3-Encryption-Options-768x280.jpg "S3")](https://mk0learndigital3jsdb.kinstacdn.com/wp-content/uploads/2019/03/Amazon-S3-Encryption-Options-768x280.jpg "S3")


https://digitalcloud.training/certification-training/aws-developer-associate/aws-storage/amazon-s3/

## KINESIS

## DYNAMO_DB
1. Cơ chế Stateless
2. Different Scan && Query?
https://dynobase.dev/dynamodb-scan-vs-query
4. Different Items && Collections?
5. DynamoDB Stream?
Hiểu basic là sync-data realtime
Có 2 thuộc tính:
- StreamEnabled — Specifies whether a stream is enabled (true) or disabled (false) for the table.
- StreamViewType — Specifies the information that will be written to the stream whenever data in the table is modified:
+ KEYS_ONLY — Only the key attributes of the modified item.
+ NEW_IMAGE — The entire item, as it appears after it was modified.
+ OLD_IMAGE — The entire item, as it appeared before it was modified.
NEW_AND_OLD_IMAGES — Both the new and the old images of the item.

6. Is DynamoDB query cheaper than scan? => Yes, why?
7. What is the DynamoDB scan vs query performance difference?
If you need to access data identified by known keys, query is much faster because of the direct access method.
7. I need to use scan. Can I make it faster? => **using DynamoDB Parallel Scan**
8. When scan periodically(định kỳ) => **Set a smaller page size for the scan**
=> Different Isolate scan operations
9. @Hoann03  => Session tính toán eventully vs strong 
![image](https://user-images.githubusercontent.com/42288000/139094493-2267054d-90da-482c-969b-0c1c5c064048.png)
![image](https://user-images.githubusercontent.com/42288000/140948293-24aa9819-bc73-45ae-bb8f-13c47ea23898.png)
![image](https://user-images.githubusercontent.com/42288000/141665406-391b4d49-f2e3-4e5d-8a2e-6130b89df378.png)
![image](https://user-images.githubusercontent.com/42288000/142008885-0d8ee925-fb20-4af2-a1f3-5bc0819371e4.png)
![image](https://user-images.githubusercontent.com/42288000/142747023-3cc7e27f-06f4-432b-82a7-bae2d78f08ed.png)

https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Limits.html


## RDS
1. is an Online Transaction Processing (OLTP) type of database.
2. Khi muốn migrate từ on-premies lên RDS nhưng db rất nặng => để re-factor => **Add a connection string to use an Amazon RDS ___read replica___ for read queries**

https://digitalcloud.training/certification-training/aws-developer-associate/aws-database/amazon-rds/

[![RDS](https://d1.awsstatic.com/asset-repository/read-replicas-scaling-disaster-recovery.3b8da7093daeb1e87426225caf49e32efe7ae01a.png "RDS")](https://d1.awsstatic.com/asset-repository/read-replicas-scaling-disaster-recovery.3b8da7093daeb1e87426225caf49e32efe7ae01a.png "RDS")

3.Khi cần multi-threaded in-memory cache ở RDS => **[ELASTICACHE](#ELASTICACHE) Memcached**


## AWS_CODEBUILD
1. **buildspec.yml**
2. Khi bị error stated that the length of all environment variables exceeds the limit for the combined maximum of characters
=> dùng (#Parameters_Store) can store an individual environment variable (name and value added together) that is a combined 4,096 characters or less.

## AWS_CODEDEPLOY
1. Preparing to deploy using CodeDeploy => **appspec.yaml**
2. appspec.yaml structure = **BeforeAllowTraffic > AfterAllowTraffic**
3. codedeploy => run order of the hooks
![image](https://user-images.githubusercontent.com/42288000/140941507-d565ca06-3c20-4751-8a18-71ca64f127fa.png)

4. Deploy: [EC2](#EC2), on-premises, [AWS LAMBDA](#AWS_LAMBDA), [ECS](#ECS)
5. Use AWS CodeDeploy to create multiple deployment groups
https://digitalcloud.training/certification-training/aws-developer-associate/aws-developer-tools/aws-codedeploy/

## CLOUD_FRONT
1. **Is a Web services**. Uses Amazon CloudFront for caching content closer to users.
2. Khi App đang xài ở trên thế giới => Dev update version => users are not able to see the latest changes.
Nguyên nhân: do cache còn lưu bản cũ => xóa edge caches đi để version mới được lưu vào cache vào lần tới (**Invalidate all the application objects from the edge caches**)
3. Different when deploy in [CLOUD FRONT](#CLOUD_FRONT) && [S3](#S3) && [EC2](#EC2) ??
https://digitalcloud.training/certification-training/aws-developer-associate/aws-networking-and-content-delivery/amazon-cloudfront/

4. Different between Origin/Viewer Request/Response 
 ![image](https://user-images.githubusercontent.com/42288000/138714323-f19dfa77-9655-46b3-96d3-097b606c24cf.png)

 ## CLOUD_FORMATION
 1. What is it?
 2. A CloudFormation stack needs to be deployed in several regions and requires a different Amazon Machine Image (AMI) in each region? => **dùng Mappings**
 Example:
```bash
    "RegionMap" : {
      "us-east-1"        : {"HVM64" : "ami-0ff8a91507f77f867", "HVMG2" : "ami-0a584ac55a7631c0c"},
      "us-west-1"        : {"HVM64" : "ami-0bdb828fd58c52235", "HVMG2" : "ami-066ee5fd4a9ef77f1"},
      "eu-west-1"        : {"HVM64" : "ami-047bb4163c506cd98", "HVMG2" : "ami-0a7c483d527806435"},
      "ap-northeast-1"   : {"HVM64" : "ami-06cd52961ce9f0d85", "HVMG2" : "ami-053cdd503598e4a9d"},
      "ap-southeast-1"   : {"HVM64" : "ami-08569b978cc4dfa10", "HVMG2" : "ami-0be9df32ae9f92309"}
    }
```

## CLOUD_WATCH
 
## CLOUD_TRAIL

## IAM
1. Different manage policy vs inline policy
2. Use IAM policy

A.	condition:

B.	principal:

C.	variables:

D.	resource: 


