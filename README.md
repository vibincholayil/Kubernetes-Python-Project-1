# Kubernetes project: Deploy application on EKS

## About the Project
It's my personal project to showcase skills in robust and highly stable K8s cluster service. The project focuses on how to deploy a real-time application on AWS-managed control plane EKS with a public IP, which will be accessed through a Application load balancer.

https://app.eraser.io/workspace/LwLmoLYNMPLvaQxNPfJC?origin=share&elements=LTa8Ornf-bD6aik7rN1lTA

### MyLearnings:
This project teaches me how to deploy an application from start to finish on AWS EKS using Fargate. It also covers the configuration of deployments, services, and pods. Additionally, I learn how to use Helm charts for managing Kubernetes applications.


## Project implimentation steps
Prerequisites: kubectl,AWS CLI, eksctl

### Install EKS with Fargate
eksctl create cluster --name demo-cluster-1 --region eu-west-2 --fargate
### update
aws eks update-kubeconfig --name demo-cluster-1 --region eu-west-2
### Creating a new fargate profile 
eksctl create fargateprofile \
--cluster demo-cluster-1 \
--region eu-west-2 \
--name alb-sample-app \
--namespace game-2048
![image](https://github.com/user-attachments/assets/9e825d44-f7a1-420f-a3f7-8480e755ef35)

### Deploy the deployment, service and Ingress
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/examples/2048/2048_full.yaml
![image](https://github.com/user-attachments/assets/a9ebd7f2-d3da-4a3f-9e68-14a17c373d8c)

### Configure IAM OIDC provider
eksctl utils associate-iam-oidc-provider --cluster demo-cluster-1 --approve
![image](https://github.com/user-attachments/assets/7571dcfc-a50c-4f68-9dfc-6921be047a1b)

### Create IAM Policy
aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json

### Create IAM Role
eksctl create iamserviceaccount \
  --cluster=demo-cluster-1 \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --role-name AmazonEKSLoadBalancerControllerRole \
  --attach-policy-arn=arn:aws:iam::307946671429:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve
![image](https://github.com/user-attachments/assets/345054dd-cc57-4b32-9501-65871984519a)

### Deploy ALB controller
helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system \
  --set clusterName=demo-cluster-1 \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=eu-west-2 \
  --set vpcId=vpc-0e71d6f189352079b
![image](https://github.com/user-attachments/assets/ee33e95c-2cf1-4e0d-bc45-d60b65bd6bec)

### Verify that the deployments are running.
kubectl get deployment -n kube-system aws-load-balancer-controller
![image](https://github.com/user-attachments/assets/b48c6e95-933b-491f-a620-353c17731b9d)

### Check the ALB Addres
kubectl get ingress -n game-2048
![image](https://github.com/user-attachments/assets/d6cc37f1-3b1d-480a-b950-cbabbfac176f)
![image](https://github.com/user-attachments/assets/dcc1beb2-500c-461e-8037-9f41c04caf21)
![image](https://github.com/user-attachments/assets/06437375-5894-466d-b381-e15bf52f8ebe)


### The Application is running
![image](https://github.com/user-attachments/assets/d0b79597-5267-46ef-9db4-a4a02d8b43a3)




