# Kubernetes End to End project on EKS

prerequisites
kubectl – A command line tool for working with Kubernetes clusters. For more information, see Installing or updating kubectl.
eksctl – A command line tool for working with EKS clusters that automates many individual tasks. For more information, see Installing or updating.

AWS CLI – A command line tool for working with AWS services, including Amazon EKS. For more information, see Installing, updating, and uninstalling the AWS CLI in the AWS Command Line Interface User Guide. After installing the AWS CLI, we recommend that you also configure it. For more information, see Quick configuration with aws configure in the AWS Command Line Interface User Guide.

aws eks update-kubeconfig --name demo-cluster --region eu-west-2

Creating fargate profile
eksctl create fargateprofile \
    --cluster demo-cluster \
    --region eu-west-2 \
    --name alb-sample-app \
    --namespace game-2048
    this will create a new profile in fargate profile session called  alb-sample-app


Following link has all the comment ,it has all comment about deployment,service and ingress
https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/examples/2048/2048_full.yaml
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/examples/2048/2048_full.yaml

IAM OIDDC provider

eksctl utils associate-iam-oidc-provider --cluster demo-cluster --approve

install a alb controller
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/install/iam_policy.json

brew install jq

aws iam create-policy policy-name AWSLoadBalancerControllerIAMPolicy policy-document file://iam_policy.json

eksctl create iamserviceaccount \
  --cluster=demo-cluster\
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --role-name AmazonEKSLoadBalancerControllerRole \
  --attach-policy-arn=arn:aws:iam::307946671429:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve

  install helm chart repo
  brew install helm
  helm repo add eks https://aws.github.io/eks-charts
  vpc-
  helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system \
  --set clusterName=demo-cluster \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=eu-west-2 \
  --set vpcId=vpc-0212b89ad0a3a9024 

kubectl get deployment -n kube-system aws-load-balancer-controller