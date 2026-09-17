# order-api
Tool that we used 
Github : for repository seperate repo code(orders-api) and config(orders-api-config)
GitOps : using (orders-api-config) repo as a gitops
Docker : for build image 
Jenkins : for CI 
ECR : iamge registry 
ArgoCD : CD
Helm Cahrt : for deploy in chart in kube

Step How it work
- git push
- Jenkins CI validate and building  [check scm, build image, smoke test image, psuh to ecr, bump tag values in gitops repo for CD]
- after gitops repo got bump tag argoCD picking it up and deploy with configuratio.


