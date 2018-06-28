# Kubernetes

---

## kubectl常用命令

```
kubectl run <name> --image=<image_name>
kubectl describe pods <pod_name>
kubectl logs <pod_name>
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
这个东西目前最新版本只支持basic auth认证，需要去掉apiserver启动参数中--admission-control中的ServiceAccount，同时加入basic auth的配置：--basic-auth-file=/etc/kubernetes/kubepasswd.csv
```



