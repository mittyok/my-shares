---
//theme : "white"
transition: "zoom"
highlightTheme: "darkula"
---

## ksonnet如何工作

![https://raw.githubusercontent.com/ksonnet/ksonnet/master/docs/img/ksonnet_overview.svg?sanitize=true](https://raw.githubusercontent.com/ksonnet/ksonnet/master/docs/img/ksonnet_overview.svg?sanitize=true)

---

## Install
```
brew install ksonnet/tap/ks
```

---

## Demo

```
ks init guestbook --context ks-dev 
```
```
ks generate deployed-service guestbook-ui \
   --image gcr.io/heptio-images/ks-guestbook-demo:0.1 \
   --type ClusterIP
```
查看生成的内容：
```
ks show default
```
可以部署了：
```
ks apply default
```

---

## 生成并部署应用

![https://ksonnet.io/images/tutorial-img-4.png](https://ksonnet.io/images/tutorial-img-4.png)

--

生成manifest

```
ks generate deployed-service guestbook-ui \
   --image gcr.io/heptio-images/ks-guestbook-demo:0.1 \
   --type ClusterIP
```

查看生成的yaml文件

```
ks show default
```

部署刚才生成的yaml文件

```
ks apply default
```




