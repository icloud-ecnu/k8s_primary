# k8s_primary
Our goal is to run the distributed tensorflow in a kubernetes cluster.Firstly,we plan to run distributed cifar10 which is included in the models module of tensorflow.

## 一、 Build docker image
We build a docker image including the image tensorflow:lateset, the models directory and our cifar10_cluster_train.py.
1. Make a dir named cifar10 including Dockerfile and dir models. 
2. cd cifar10
3. docker build -t your_docker_account_name/your_repositories_name:tag  .
4. docker push your_docker_account_name/your_repositories_name:tag
Then,we can pull the image we build from our docker hub account when we define rc.yaml.

## 二、 Create a persistent volume for the cluster
You can google how to build a nfs cluster according to the os you use.Here are some tips:
1. Make sure your firewalld service is turned off and selinux is permissive ("getenforce" on centos).
2. If you seek in some trouble hard to deal with ,run"iptables -P FORWARD ACCEPT" maybe amazing.
3. kubectl create -f (the files we give)

## 三、 Create services and replicaset for the application.
1. kubectl create -f myjob.template      # run it on the master host.We define 4 services for 4 pods.
2. kubectl get pods -o wide     # get the name and status of your pods and which node each pod was scheduled on.
3. kubectl describe pods pod_name     # get the details information of the pod process.
4. kubectl logs pod_name    # get logs of the first container in the pod.
5. kubectl get pods | grep Evicted | awk '{print $1}' | xargs kubectl delete pod    # delete all evicted pods
6. kubectl delete replicaset cifar10-ps-0\\\\
   kubectl delete replicaset cifar10-worker-0//
   kubectl delete replicaset cifar10-worker-1\\
   kubectl delete replicaset cifar10-worker-2\\
   kubectl delete services cifar10-ps-0\\
   kubectl delete services cifar10-worker-0\\
   kubectl delete services cifar10-worker-1\\
   kubectl delete services cifar10-worker-2     # delete the services and replicaset,it can save some time for you.
7. kubectl exec -it pod_name sh(/bin/bash)      # enter terminal of the first container.
8. Train_dir is in the dir /tmp/cifar10_train.
9. We can get the stdout stream of the container in the dir /media ,for we redirect it to /data/worker+task_inedx  in our py file and it mounts on the dir /media which is defined in template. 
