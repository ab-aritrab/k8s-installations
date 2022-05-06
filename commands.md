
```
sudo kubectl get nodes
sudo kubectl get pods --all-namespaces
sudo kubectl get nodes
sudo kubectl get pods -o wide --all-namespaces
kubectl get namespaces
kubectl get namespaces kube-system

kubectl create namespace devops-demo
kubectl describe namespaces devops-demo
kubectl describe namespaces default
kubectl describe namespaces kube-system

kubectl get namespaces --all
kubectl get namespaces
mkdir k8s-deployment
cd k8s-deployment/

vim deployment.yaml
kubectl apply -f deployment.yaml
kubectl get deployments
kubectl get deployments -o wide
kubectl delete -f deployment.yaml
kubectl apply -f deployment.yaml
kubectl get services
kubectl expose deployment nginx-deployment --type=LoadBalancer --name=nginx-web-server
kubectl get services
svc-nginix.yaml
cat deployment.yaml
vim svc-nginix.yaml
kubectl apply -f svc-nginix.yaml
vim svc-nginix.yaml
kubectl apply -f svc-nginix.yaml
kubectl get services

```

### Delete namespaces & pods =======>
https://stackoverflow.com/questions/33509194/command-to-delete-all-pods-in-all-kubernetes-namespaces

### Deployments =====>
https://kubernetes.io/docs/concepts/workloads/controllers/deployment/


### Services =====>
https://stackoverflow.com/questions/44110876/kubernetes-service-external-ip-pending
https://kubernetes.io/docs/concepts/services-networking/connect-applications-service/


### PODS with Static IP =======> 
https://stackoverflow.com/questions/41566655/how-to-assign-a-static-ip-to-a-pod-using-kubernetes