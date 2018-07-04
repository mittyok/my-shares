---
transition: "zoom"
highlightTheme: "darkula"
---

## dnsmaq的使用

```
brew install dnsmasq
sudo brew services start dnsmasq
sudo vi /usr/local/etc/dnsmasq.conf ## add 'address=/<domain>/<ip>'
sudo brew services restart dnsmasq
sudo mkdir -p /etc/resolver
sudo touch /etc/resolver/<domain>
sudo vi /etc/resolver/<domain> ## add 'nameserver 127.0.0.1'
```
