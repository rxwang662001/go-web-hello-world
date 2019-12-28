https://gitlab.rnd.gic.ericsson.se/raket/raket/blob/master/DEMO.md
demo homework procedure:

Task 0: Install a ubuntu 16.04 server 64-bit
  --on personal PC install virutalbox from software center
  --download ubuntu-16.04.6-server-amd64.iso
  --create a virutal machine from virutalbox
  --add NAT for this virutal machine through settings->network

Task 1: Update system
--update system:
   sudo apt update
   sudo apt upgrade -y
   reboot
--update kernel
wget http://kernel.ubuntu.com/~kernel-ppa/mainline/v5.4/linux-headers-5.4.0-050400_5.4.0-050400.201911242031_all.deb                                                       
wget http://kernel.ubuntu.com/~kernel-ppa/mainline/v5.4/linux-headers-5.4.0-050400-generic_5.4.0-050400.201911242031_amd64.deb
wget http://kernel.ubuntu.com/~kernel-ppa/mainline/v5.4/linux-image-unsigned-5.4.0-050400-generic_5.4.0-050400.201911242031_amd64.deb   

export http_proxy=http://www-proxy.ericsson.se:8080 
 
Task 2: install gitlab-ce version in the host

sudo EXTERNAL_URL="http://10.0.2.15" apt-get install gitlab-ee
sudo vi /etc/gitlab/gitlab.rb
sudo gitlab-ctl reconfigure
then below file will be changed:
sudo vi /opt/gitlab/embedded/service/gitlab-rails/config/gitlab.yml
sudo gitlab-ctl restart

http://10.210.149.42:8888/demo/go-web-hello-world   root/beijing123  (no proxy)
http://127.0.0.1:8080/demo/go-web-hello-world   root/beijing123

Task 3, 4, 5, 6:  build the app and expose ($ go run) the service to 8081 port

mkdir -p $HOME/go/src/hello 
vi main.go 
go build   
./hello 
http://10.210.149.42:8100/

git clone https://gitlab.rnd.gic.ericsson.se/raket/demo/go-web-hello-world.git 
docker build -t go-web-hello-world . 
docker images 
docker run -p 8100:8100 -it --rm --name run-web-hello-world go-web-hello-world 

 go build -o myexec main.go lib.go
 go build -o main-out main.go

docker build -t  rxwang662001/go-web-hello-world:v0.1  .

Task 7: push image to dockerhub
docker build -t  rxwang662001/go-web-hello-world:v0.1  .
sudo docker login --username=rxwang662001
sudo docker push rxwang662001/go-web-hello-world

https://hub.docker.com/repository/docker/rxwang662001/go-web-hello-world

docker tag go-web-hello-world:latest armdocker.rnd.ericsson.se/sandbox/go-web-hello-world:wrx-0.0.1
docker login armdocker.rnd.ericsson.se
docker push armdocker.rnd.ericsson.se/sandbox/go-web-hello-world:wrx-0.0.1

sudo docker images
REPOSITORY                                             TAG                 IMAGE ID            CREATED             SIZE
<none>                                                 <none>              19ed8285303e        2 days ago          400MB
armdocker.rnd.ericsson.se/sandbox/go-web-hello-world   wrx-0.0.1           51ae1c0eca6e        2 days ago          7.39MB
go-web-hello-world                                     latest              51ae1c0eca6e        2 days ago          7.39MB
rxwang662001/go-web-hello-world                        v0.1                51ae1c0eca6e        2 days ago          7.39MB
golang                                                 alpine              69cf534c966a        3 weeks ago         359MB


Task 8: document the procedure in a MarkDown file

create README.md in my gitlab

Task 9: install a single node Kubernetes cluster using kubeadm

kubeadm init

Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 10.210.149.120:6443 --token uns2o3.b812ine7pnjdwbs9 \
    --discovery-token-ca-cert-hash sha256:c196fd91a2bc4bcd26688e9f0ef07d048bd09c2d1fc4018f91f78cf0640ea883

kubectl get pod -A
NAMESPACE     NAME                                          READY   STATUS    RESTARTS   AGE
kube-system   coredns-5c98db65d4-fkz9z                      0/1     Pending   0          9m20s
kube-system   coredns-5c98db65d4-ncmr2                      0/1     Pending   0          9m20s
kube-system   etcd-node-10-210-149-120                      1/1     Running   0          8m11s
kube-system   kube-apiserver-node-10-210-149-120            1/1     Running   0          8m12s
kube-system   kube-controller-manager-node-10-210-149-120   1/1     Running   0          8m19s
kube-system   kube-proxy-kw5dj                              1/1     Running   0          9m20s
kube-system   kube-scheduler-node-10-210-149-120            1/1     Running   0          8m12s

kubectl apply -f https://docs.projectcalico.org/v3.8/manifests/calico.yaml

[root@node-10-210-149-120 ~]# kubectl get pod -A
NAMESPACE     NAME                                          READY   STATUS    RESTARTS   AGE
kube-system   calico-kube-controllers-5bbc8f45cb-g6qdm      1/1     Running   0          65s
kube-system   calico-node-66n2p                             1/1     Running   0          65s
kube-system   coredns-5c98db65d4-fkz9z                      1/1     Running   0          11m
kube-system   coredns-5c98db65d4-ncmr2                      1/1     Running   0          11m
kube-system   etcd-node-10-210-149-120                      1/1     Running   0          10m
kube-system   kube-apiserver-node-10-210-149-120            1/1     Running   0          10m
kube-system   kube-controller-manager-node-10-210-149-120   1/1     Running   0          10m
kube-system   kube-proxy-kw5dj                              1/1     Running   0          11m
kube-system   kube-scheduler-node-10-210-149-120            1/1     Running   0          10m
[root@node-10-210-149-120 ~]#

[root@node-10-210-149-120 ~]# kubectl taint nodes --all node-role.kubernetes.io/master-
node/node-10-210-149-120 untainted
[root@node-10-210-149-120 ~]# kubectl get nodes
NAME                  STATUS   ROLES    AGE   VERSION
node-10-210-149-120   Ready    master   12m   v1.15.3

Task 10: deploy the hello world container

kubectl create namespace wrx
kubectl run go-web-hello-world --image=armdocker.rnd.ericsson.se/sandbox/go-web-hello-world:wrx-0.0.1 --replicas=1
kubectl expose deployment go-web-hello-world --external-ip="10.210.59.74" --port=8100 --target-port=8100


Task 11: install kubernetes dashboard

kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-beta8/aio/deploy/recommended.yaml

Task 12: generate token for dashboard login in task 11
https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md
Bearer Token:
[root@node-10-210-149-120 ~]# kubectl -n kubernetes-dashboard describe secret $(kubectl -n kubernetes-dashboard get secret | grep admin-user | awk '{print $1}')
Name:         admin-user-token-gm5f6
Namespace:    kubernetes-dashboard
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: admin-user
              kubernetes.io/service-account.uid: c85aa07a-d450-4b2e-babd-2248fdcbfa0c

Type:  kubernetes.io/service-account-token

Data
====
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6IiJ9.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJhZG1pbi11c2VyLXRva2VuLWdtNWY2Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImFkbWluLXVzZXIiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiJjODVhYTA3YS1kNDUwLTRiMmUtYmFiZC0yMjQ4ZmRjYmZhMGMiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZXJuZXRlcy1kYXNoYm9hcmQ6YWRtaW4tdXNlciJ9.662GCzuqPexnN7sb7e4FsVpa51PcASKsMM46VSSAzJfRWMpma-xZJa47_qoXBk1az-0JdxbfEmBJUnGTcMpPLTg2EellJE-b1hk1_Cjz3_cC4x2tHue1WwrhB4VGZGAOgiWZptbRSJLS-8QnirAAPtlH9QAvzA9p7Gbmk7P1tffvuD4-S-IAtvcNP0JxT8yJZQYMWyvNYNClTGy3W7Dp5DdIfU3d1APzCs17s3qS7qWYI-G4zL3vY1F2mAyxPOQ_skFOb3KhC_P_CYx3qbq9BzwyYCZOnyOe7_W_F467Z8vVcxG4cftVku6MoEF89TVHs51JIcsroYyh2pIZige14g
ca.crt:     1025 bytes
namespace:  20 bytes

http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/


