Menu
=============
* [docker](#): Lab document
* [Deploy](#DEPLOY): Deploy
* [ECR](#ECR) 
* [Parameters Store](#Parameters_Store) 
* [ECS](#ECS) 
* [EKS](#EKS) 
* [EBS](#EBS) 
* [API GATEWAY](#API_GATEWAY) 
* [ROUTE 53](#ROUTE_53) 
* [AWS LAMBDA](#AWS_LAMBDA) 


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

1. Khi AWS CodeBuild limit environment variables (all names and values added together) => dùng Parameter Store

## ECS
https://aws.amazon.com/vi/premiumsupport/knowledge-center/create-alb-auto-register/
=> Config network -> Create EC2 -> Create Target Group -> Create LoadBalancer với 2 EC2 -> Tạo Task trong ECS sử dụng LoadBalancer -> Tạo Cluster -> Tạo service -> gắn lb nảy tạo vào


## EKS

## EBS

## API_GATEWAY
1. Cơ chế catche
https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-caching.html

## ROUT_E53


## AWS_LAMBDA
1. AWS Lambda is a stateless compute service
