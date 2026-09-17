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

prerequisites and step
- github repo code[https://github.com/Winniepoom/orders-api] and gitops[https://github.com/Winniepoom/order-api-config]
- Jenkins pipline setup Branch Sources aim to code repo and use 'githun-pat' as credential and also fine grin tokens to read repo setup pipeline with webhook from github, aslo add credential for write content to make it be able to write and bump tag for gitops repo.
- AWS ECR you need to login with 'aws login' and run "
kubectl create secret docker-registry ecr-pull-secret \
  --docker-server=458818121281.dkr.ecr.ap-southeast-1.amazonaws.com \
  --docker-username=AWS \
  --docker-password=$(aws ecr get-login-password --profile ci-product-demo --region ap-southeast-1) \
  --docker-email=ci@example.com \
  --dry-run=client -o yaml | kubectl apply -f -
  " 
  for create ecr-pull-secret and be able to pull image from ecr
  - Argo CD setup point  applicaton repo to gitops and also path at "charts/orders-api " enable self heal, auto sync and prune 

