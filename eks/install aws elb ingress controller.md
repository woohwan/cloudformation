```
curl -o iam_policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.4.2/docs/install/iam_policy.json

aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json


eksctl utils associate-iam-oidc-provider --region=ap-northeast-2 --cluster=dev --approve

** On linux
AccountId=$(aws sts get-caller-identity --query='Account' --output=text)
VpcId=$(aws ec2 describe-vpcs --query 'Vpcs[?Tags[?Key==`Name`]|[?Value==`eks-vpc`]].VpcId' --output text)

aws eks update-kubeconfig --name dev --region ap-northeast-2

eksctl create iamserviceaccount \
  --cluster=dev \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --role-name "AmazonEKSLoadBalancerControllerRole" \
  --attach-policy-arn=arn:aws:iam::${AccountId}:policy/AWSLoadBalancerControllerIAMPolicy \
  --override-existing-serviceaccounts \
  --region ap-northeast-2 \
  --approve

helm repo add eks https://aws.github.io/eks-charts
helm repo update

kubectl apply -k "github.com/aws/eks-charts/stable/aws-load-balancer-controller//crds?ref=master"

helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
  -n kube-system \
  --set clusterName=dev \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=ap-northeast-2 \
  --set vpcId=${VpcId}


** On Windows

aws eks update-kubeconfig --name dev --region ap-northeast-2

$AccountId=$(aws sts get-caller-identity --query='Account' --output=text)
$VpcId=$(aws ec2 describe-vpcs --query 'Vpcs[?Tags[?Key==`Name`]|[?Value==`eks-vpc`]].VpcId' --output text)

eksctl create iamserviceaccount `
  --cluster=dev `
  --namespace=kube-system `
  --name=aws-load-balancer-controller `
  --role-name "AmazonEKSLoadBalancerControllerRole" `
  --attach-policy-arn=arn:aws:iam::${AccountId}:policy/AWSLoadBalancerControllerIAMPolicy `
  --override-existing-serviceaccounts `
  --region ap-northeast-2 `
  --approve

choco install kubernetes-helm

helm repo add eks https://aws.github.io/eks-charts
helm repo update

helm install aws-load-balancer-controller eks/aws-load-balancer-controller `
  -n kube-system `
  --set clusterName=dev `
  --set serviceAccount.create=false `
  --set serviceAccount.name=aws-load-balancer-controller `
  --set region=ap-northeast-2 `
  --set vpcId=${VpcId}




kubectl get deployment -n kube-system aws-load-balancer-controller
kubectl logs -n kube-system   deployment.apps/aws-load-balancer-controller


----------------------------------------------
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-nginx
  annotations:
    alb.ingress.kubernetes.io/scheme: internet-facing
    alb.ingress.kubernetes.io/target-type: instance
spec:
  ingressClassName: alb
  rules:
    - http:
        paths:
        - path: /
          pathType: Prefix
          backend:
            service:
              name: nginx
              port:
                number: 80
```
