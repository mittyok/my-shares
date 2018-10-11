---
//theme : "white"
transition: "zoom"
highlightTheme: "darkula"

---

# Kubernetes

---

## 组件
* controller-manager
    * 通过apiserver管理集群的整个状态，比如某个节点down掉了，manager会负责把它拉起来

---

## kubectl常用命令

```
kubectl run <name> --image=<image_name>
kubectl describe pods <pod_name>
kubectl logs <pod_name>
```

---

## 在线调整环境变量

使用：kubectl-set-env
https://www.mankier.com/1/kubectl-set-env

--

### DEMO

```bash 
  # Update deployment 'registry' with a new environment variable
  kubectl set env deployment/registry STORAGE_DIR=/local
  
  # List the environment variables defined on a deployments 'sample-build'
  kubectl set env deployment/sample-build --list
  
  # List the environment variables defined on all pods
  kubectl set env pods --all --list
  
  # Output modified deployment in YAML, and does not alter the object on the server
  kubectl set env deployment/sample-build STORAGE_DIR=/data -o yaml
  
  # Update all containers in all replication controllers in the project to 
  have ENV=prod
  kubectl set env rc --all ENV=prod
  
  # Import environment from a secret
  kubectl set env --from=secret/mysecret deployment/myapp
  
  # Import environment from a config map with a prefix
  kubectl set env --from=configmap/myconfigmap --prefix=MYSQL_
   deployment/myapp
  
  # Remove the environment variable ENV from container 'c1' in all deployment configs
  kubectl set env deployments --all --containers="c1" ENV-
  
  # Remove the environment variable ENV from a deployment definition on disk and
  # update the deployment config on the server
  kubectl set env -f deploy.json ENV-
  
  # Set some of the local shell environment into a deployment config on the server
  env | grep RAILS_ | kubectl set env -e - deployment/registry
```
<!-- .element style="font-size: 18px;" -->

---

## Certificate & Authentication

* 认证：我需要知道你是谁
* 授权：知道你是谁后，我才能给你权利

--

容器内进程访问需要通过ServiceAccount进行授权，需要注意几点：
* apiserver启动时一定要有--admission-control=AlwaysAdmit,ServiceAccount
* 通过get pod <pod_name> -o yaml可以看到对应的service account信息
* 如果使用serviceaccount无法访问apiserver时（401），尝试作如下处理

```
1: 重新启动apiserver
2: 确认相关模板文件中没有依赖--tls-cert-file或者--tls-private-key-file，如果依赖了，看是否需要和apiserver的证书一致，一般需要一致的，是具体应用的访问形式而定。如果是属于类似apiserver的访问形式，依赖于service account的token，就需要使用相同的证书和私钥
```

---

## Gitlab install
```
helm init
helm repo add gitlab https://charts.gitlab.io
helm install --name gitlab --set gitlab=CE gitlab/gitlab-omnibus
```

---

## 好工具

--

* kube-shell
> 方便使用kubernetes cli

--

* cockpit/kubernetes
> 比较好的一个管理UI，感觉比kubernetes本身的dashboard的体验要好很多

---

## 坑

--

<!-- .slide: style="text-align: left;" -->
问题：
```
did you specify the right host or port
```

解决：
```
sudo cp /etc/kubernetes/admin.conf $HOME/
sudo chown $(id -u):$(id -g) $HOME/admin.conf
export KUBECONFIG=$HOME/admin.conf
```

--

<!-- .slide: style="text-align: left;" -->
问题：
```
stat /home/vagrant/.kube/config: no such file or directory
```

解决：
```
export KUBECONFIG=$HOME/admin.conf
cp $HOME/admin.conf $HOME/.kube/config
```

--

<!-- .slide: style="text-align: left;" -->
问题：
```
Negotiate failed: Couldn't get api version: 401 Unauthorized
Couldn't get api version: 401 Unauthorized
```

解决：
```
这个东西目前最新版本只支持basic auth认证，需要去掉apiserver启动参数中
--admission-control中的ServiceAccount，同时加入basic auth的配置：
--basic-auth-file=/etc/kubernetes/kubepasswd.csv，
对应/etc/kubernetes/kubepasswd.csv中的内容，类似：
password,username,uid
```

--

<!-- .slide: style="text-align: left;" -->
问题：
```
unable to do port forwarding: socat not found.
```

解决：
```
install linux tool socat.
```

--

<!-- .slide: style="text-align: left;" -->
问题：
```
dial tcp 127.0.0.1:8080: connect: connection refused
```

解决：
```
这个问题是由于tiller本身部署到了k8s中，直接使用localhost:8080肯定是
解决不了问题的，需要让tiller知道kubernetes master的位置：
KUBERNETES_MASTER=<ip:port>
```

--

<!-- .slide: style="text-align: left;" -->
问题：
```
ingress如何支持对后端https服务的代理支持（nginx)
```

解决：
* 需要单独的ingress config，因为需要在annotation中加入相关与http冲突性的配置
* 创建ingress的文件内参考如下：
```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  annotations:
    ingress.kubernetes.io/force-ssl-redirect: "true"
    ingress.kubernetes.io/ssl-passthrough: "true"
    ingress.kubernetes.io/ssl-redirect: "false"
    kubernetes.io/ingress.class: nginx
    ## 这个很关键，起着决定作用，通过ssl的方式访问后端
    nginx.ingress.kubernetes.io/secure-backends: "true"
  creationTimestamp: 2018-07-03T03:52:17Z
  generation: 3
  name: kubernetes-cockpit
  namespace: default
  resourceVersion: "315584"
  selfLink: /apis/extensions/v1beta1/namespaces/default/ingresses/kubernetes-cockpit
  uid: 7a9887a2-7e74-11e8-b386-0800278dc04d
spec:
  backend:
    serviceName: kubernetes-cockpit
    servicePort: 443
  tls:
  - hosts:
    - cockpit.jdevops.com
status:
  loadBalancer:
    ingress:
    - {}
```

--

<!-- .slide: style="text-align: left;" -->
问题：
```
specified privileged container, but is disallowed
```

解决：
```
apiserver kubelet启动时都需要加上--allow-privileged=true
```

--

<!-- .slide: style="text-align: left;" -->
问题：
```
coredns stubdomains proxy config
```

解决：
```
.:53 {
        errors
        health
        kubernetes cluster.local  in-addr.arpa ip6.arpa {
           upstream  8.8.8.8 8.8.4.4
           pods insecure
           fallthrough in-addr.arpa ip6.arpa
        }
        federation cluster.local {
           foo foo.feddomain.com
        }
        prometheus :9153
        proxy .  8.8.8.8 8.8.4.4
        cache 30
    }
    abc.com:53 {
        errors
        cache 30
        proxy . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        proxy . 2.3.4.5
    }
```

--

<!-- .slide: style="text-align: left;" -->
问题：
```
grafana export all dashboards at once
```

解决：
```
参考：https://blog.dictvm.org/export-all-grafana-graphs/
```

--

<!-- .slide: style="text-align: left;" -->
问题：
```
kubectl top pod resources
```

解决：
```
使用heapster，参考：
https://www.mankier.com/1/kubectl-top-pod
```

--

<!-- .slide: style="text-align: left;" -->
问题：
```
kubectl connect remote cluster
```

解决：
```
kubectl config set-cluster <cluster-name> --server=<server-url>
kubectl config set-context <context-name> --cluster=<cluster-name>
kubectl config use-context <context-name>
```

