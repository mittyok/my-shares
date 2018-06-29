---
//theme : "white"
transition: "zoom"
highlightTheme: "darkula"

---

# Kubernetes

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



