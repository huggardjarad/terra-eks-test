# EKS terraform playground

## Useful Commands

* kubectl apply -f <yaml name>
* kubectl get svc
* kubectl describe pod
* aws eks --region $(terraform output -raw region) update-kubeconfig --name $(terraform output -raw cluster_name)



