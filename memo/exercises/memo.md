## 概要
[CKAD Exercises](https://github.com/dgkanatsios/CKAD-exercises)をやってみてのメモ

## Core Concepts 
* Create a namespace called 'mynamespace' and a pod with image nginx called nginx on this namespace
```
$ kubectl create namespace mynamespace
$ kubectl run nginx --image=nginx --restart=Never -n mynamespace
```
* Create the pod that was just described using YAML
```
$ kubectl run nginx --image=nginx --restart=Never --dry-run=client -o yaml > nginx_pod.yaml
$ kubectl create -f nginx_pod.yaml -n mynamespace 
```
* Create a busybox pod (using kubectl command) that runs the command "env". Run it and see the output
```
$ kubectl run busybox -it --image=busybox --command --restart=Never -- env
```
* Create a busybox pod (using YAML) that runs the command "env". Run it and see the output
```
$ kubectl run busybox --image=busybox --restart=Never --dry-run -o yaml --command -- env > envpod.yaml
```
* Get pods on all namespaces
```
$ kubectl get pods --all-namespaces
```
* Create a pod with image nginx called nginx and allow traffic on port 80
```
$ kubectl run nginx --image=nginx --restart=Never --port=80
```
* Change pod's image to nginx:1.7.1. Observe that the pod will be killed and recreated as soon as the image gets pulled
```
$ kubectl set image pod/nginx nginx=nginx:1.7.1
```
* Get nginx pod's ip created in previous step, use a temp busybox image to wget its '/'
```
$ kubectl get po nginx -o wide
$ kubectl run busybox --it --rm --image=busybox --restart=Never -- wget -O- 10.1.1.131:80
```
* Execute a simple shell on the nginx pod
```
$ kubectl exec -it nginx -- /bin/bash
```
* Create a busybox pod that echoes 'hello world' and then exits
```
$ kubectl run busybox -it --rm --image=k8spracticalguide/busybox --restart=Never echo "hello world"
```
* Create an nginx pod and set an env value as 'var1=val1'. Check the env value existence within the pod
```
$ kubectl run nginx --image=nginx --restart=Never --env=var1=val1
$ kubectl exec -it nginx -- env
```

## Multi-container Pods
multi-container-pod.yaml

## Pod design
* Show all labels of the pods
```
$ kubectl get po --show-labels
```
* Change the labels of pod 'nginx2' to be app=v2
```
$ kubectl label po nginx2 app=v2 --overwrite
```
* Get the label 'app' for the pods
```
$ kubectl get po -L app
```
* Check how the deployment rollout is going
```
$ kubectl rollout status deploy nginx
```
* Return the deployment to the second revision (number 2) and verify the image is nginx:1.7.9
```
$ kubectl rollout undo deploy nginx --to-revision=2
```
* Autoscale the deployment, pods between 5 and 10, targetting CPU utilization at 80%
```
$ kubectl autoscale deploy nginx --min=5 --max=10 --cpu-percent=80
```
* Create a job with image perl that runs the command with arguments "perl -Mbignum=bpi -wle 'print bpi(2000)'
```
$ kubectl create job pi --image=perl -- perl -Mbignum=bpi -wle 'print bpi(2000)'
```
* Create a job with the image busybox that executes the command 'echo hello;sleep 30;echo world'
```
$ kubectl create job busybox --image=busybox -- /bin/sh -c 'echo hello;sleep 30;echo world'
```

## Configuration
* Create a configmap named config with values foo=lala,foo2=lolo
```
$ kubectl create configmap config --from-literal=foo=lala, --from-literal=foo2=lolo
```

