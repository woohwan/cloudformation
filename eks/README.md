# Create EKS using Cloudformation (amazon template)  

### Cloudformation Template 이용 방법  
https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/resource-type-schemas.html 에서  json shema zip파일을 받거나 또는  
https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-resource-specification.html 에서 one file 로 받아서  
vscode workspace settings.json 파일에 아래와 같이 작성한다.  
```  
{
  "yaml.schemas": {
    "https://d1ane3fvebulky.cloudfront.net/latest/gzip/CloudFormationResourceSpecification.json": [
      "file:///c%3A/workspace/aws/cloudformation/eks/eks-cluster.yaml",
      "file:///c%3A/workspace/aws/cloudformation/eks/test.yaml"
    ]
  }
}
```

또는 https://github.com/aws-cloudformation/cloudformation-template-schema/blob/master/docs/vscode/instructions.md 참고  

kubernete의 경우,  
```  
{
  "yaml.schemas": {
    "kubernetes": "*.ymal",
  }
}
 


self-managed nodegroup 생성 후, 아래 과정 수행  
```  
aws eks update-kubeconfig --region region-code --name my-cluster
```  