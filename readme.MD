# Ingress controller set up
```
Download OIDC provider
```
eksctl utils associate-iam-oidc-provider \
    --region us-east-1 \
    --cluster expenss-cluster-1 \
    --approve

Download IAM policy for load balancer controller

curl -o iam-policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.10.0/docs/install/iam_policy.json

Create IAM policy downloded earliar

aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam-policy.json

Create a Sevice account for ingress

eksctl create iamserviceaccount \
--cluster=expense-cluster-1 \
--namespace=kube-system \
--name=aws-load-balancer-controller \
--attach-policy-arn=arn:aws:iam::586794480827:policy/AWSLoadBalancerControllerIAMPolicy \
--override-existing-serviceaccounts \
--region us-east-1 \
--approve

Add a helm repo for eks load balancer controller

helm repo add eks https://aws.github.io/eks-charts

Helm install load balacer controller

helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=expense-cluster-1 --set serviceAccount.create=false --set serviceAccount.name=aws-load-balancer-controller