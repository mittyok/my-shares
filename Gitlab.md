---
theme : "white"
transition: "zoom"
highlightTheme: "darkula"
---

## 工具

--

### lab
```
Homebrew
brew install zaquestion/tap/lab
Bash
Installs lab into /usr/local/bin/

curl -s https://raw.githubusercontent.com/zaquestion/lab/master/install.sh | bash
Source
Required

Go 1.9+
GOPATH
dep
go get -u -d github.com/zaquestion/lab
cd $GOPATH/src/github.com/zaquestion/lab
dep ensure
go install -ldflags "-X \"main.version=$(git  rev-parse --short=10 HEAD)\""  github.com/zaquestion/lab
or

make install
```