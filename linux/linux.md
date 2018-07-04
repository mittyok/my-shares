---
transition: "zoom"
highlightTheme: "darkula"
---

## resolv.conf

```
nameserver  192.168.3.1
nameserver  192.168.3.2
options rotate
options timeout:1 attempts:2
```
*注意：*rotate: 在多个dns server中轮询；timeout：等待dns服务的响应时间；attempts：解析域名的尝试次数