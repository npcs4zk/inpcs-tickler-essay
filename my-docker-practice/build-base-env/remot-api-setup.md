# 构建Romot Api环境

### _前言_

本文记录docker怎么打开api remote接口设置，docker的版本更新太快了，不同的版本之间，设置可能不同，本文是针对docker13.1

### _Ubuntu14.04下构建docker-ce环境_

#### 查看配置文件位于哪里

```
systemctl show --property=FragmentPath docker
```

#### _编辑配置文件内容，接收所有ip请求_

```
vim  /lib/systemd/system/docker.service 
ExecStart=/usr/bin/dockerd -H unix:///var/run/docker.sock -H tcp://0.0.0.0:5678
```

![](/my-docker-practice/build-base-env/static/remotApi测试.png)

#### _重新加载配置文件，重启docker daemon_

```
sudo systemctl daemon-reload 
sudo systemctl restart docker
```

#### _命令测试_

```
inpcs@inpcshome:~$ sudo docker -H localhost:5678 version
Client:
 Version:    18.03.0-ce
 API version:    1.37
 Go version:    go1.9.4
 Git commit:    0520e24
 Built:    Wed Mar 21 23:11:46 2018
 OS/Arch:    linux/amd64
 Experimental:    false
 Orchestrator:    swarm

Server:
 Engine:
  Version:    18.03.0-ce
  API version:    1.37 (minimum version 1.12)
  Go version:    go1.9.4
  Git commit:    0520e24
  Built:    Wed Mar 21 23:10:17 2018
  OS/Arch:    linux/amd64
  Experimental:    false
```

![](/my-docker-practice/build-base-env/static/remotApi.png)

#### _页面测试_

inpcs@inpcshome:~$ curl [http://localhost:5678/version](http://localhost:5678/version)

```
{"Platform":{"Name":""},"Components":[{"Name":"Engine","Version":"18.03.0-ce","Details":{"ApiVersion":"1.37","Arch":"amd64","BuildTime":"2018-03-21T23:10:17.000000000+00:00","Experimental":"false","GitCommit":"0520e24","GoVersion":"go1.9.4","KernelVersion":"4.14.0-deepin2-amd64","MinAPIVersion":"1.12","Os":"linux"}}],"Version":"18.03.0-ce","ApiVersion":"1.37","MinAPIVersion":"1.12","GitCommit":"0520e24","GoVersion":"go1.9.4","Os":"linux","Arch":"amd64","KernelVersion":"4.14.0-deepin2-amd64","BuildTime":"2018-03-21T23:10:17.000000000+00:00"}
```



