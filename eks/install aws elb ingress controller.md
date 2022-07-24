```
curl -o iam_policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.4.2/docs/install/iam_policy.json

aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json


eksctl utils associate-iam-oidc-provider --region=ap-northeast-2 --cluster=dev --approve

AccountId=$(aws sts get-caller-identity --query='Account' --output=text)

eksctl create iamserviceaccount \
  --cluster=dev \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --role-name "AmazonEKSLoadBalancerControllerRole" \
  --attach-policy-arn=arn:aws:iam::${AccountId}:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve

aws eks update-kubeconfig --name dev --region ap-northeast-2


helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
  -n kube-system \
  --set clusterName=dev \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=ap-northeast-2 \
  --set vpcId=vpc-00bcc62f7608a51a7

```
