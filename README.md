# 如何解决git操作慢的要死甚至偶尔出现connection reset的问题

## 建设代理服务器

推荐购买阿里云服务器（地址选择香港），然后使用

```
ssh -D 8080 root@<你的服务器IP>
```

就得到一个地址位于``127.0.0.1``的socks5代理，其中``8080``应换成你想用的port，不应与其他在用的port冲突.

优点是快，缺点是要钱，你也可以使用其他代理例如[XX-net](https://github.com/XX-net/XX-net)

## 使用代理服务器进行git操作

### 使用HTTPS clone (推荐):

```
git config --global http.proxy socks5://127.0.0.1:8080
```

这里8080是port，socks5是代理类型

```
git clone https://github.com/<你的用户名>/<你的项目名>
```

如果使用XX-net请使用SOCKS5 smart-router，HTTP代理如果是使用自签CA证书可能会在这一步出错.

### 如果一定要使用SSH clone:

请先阅读[添加SSH KEY](https://docs.github.com/en/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)
**XX-net尚不支持port 22，请raise issue**

#### Windows:

感谢(https://stackoverflow.com/questions/16672351/ssh-to-a-remote-host-that-is-not-reachable-directly)

使用Cygwin，安装socat,git,openssh包 然后在ssh设置（一般位于~/.ssh/config）中添加：
```
Host github.com
User git
  ProxyCommand socat - SOCKS4:127.0.0.1:%h:%p,socksport=8080
  IdentityFile <这里填你的ssh key路径, 注意比如E盘在Cygwin下是/cygdrive/e/>
```
如果使用HTTP Proxy，则将``SOCKS4``换成``PROXY``，``socksport``换成``proxyport``

#### Linux:

在``~/.ssh/config``中添加
```
Host github.com       
  User git
  ProxyCommand nc -x <填SOCKS代理地址>:<填SOCKS5代理port> %h %p
```
如果使用HTTP PROXY, 则在``-x``之前添加``-X connect``

#### 最后clone:

```
git clone git@github.com:<你的用户名>/<你的项目名>
```
