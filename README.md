# 如何解决git操作慢的要死甚至偶尔出现connection reset的问题

## 建设代理服务器

推荐购买阿里云服务器（地址选择香港），然后使用

```
ssh -D 8080 root@<你的服务器IP>
```

就得到一个地址位于127.0.0.1的socks5代理，其中8080应换成你想用的port，不应与其他在用的port冲突.

优点是快，缺点是要钱，你也可以使用其他代理例如[XX-net](https://github.com/XX-net/XX-net)

## 使用代理服务器进行git操作

### 使用HTTPS clone:

```
git config --global http.proxy socks5://127.0.0.1:8080
```

这里8080是port，socks5是代理类型

```
git clone https://github.com/<你的用户名>/<你的项目名>
```

请使用SOCKS5，HTTP代理如果是使用自签CA证书可能会在这一步出错.

### 如果一定要使用SSH clone:

#### Windows: 暂无好的方案

理论上应该：安装[ncat](https://nmap.org/download.html#windows 选第一个加粗的.exe)，然后在ssh设置（一般位于~/.ssh/config，其中~是你的msysgit，cygwin，或其他什么你的git的模拟UNIX环境的主目录下）中添加

```
Host github.com
  User git
  ProxyCommand ncat --proxy <你的代理地址>:<你的代理port> --proxy-type <这里填http或socks4或socks5> %h %p
```

但是目前这种方法会出错，见(https://superuser.com/questions/1615110/git-pushing-through-socks5-proxy-with-proxycommandncat-fails?newreg=dba074bddecb45f58879473bd65b3f44)

#### Linux:

##### 使用上述阿里云：
在``.ssh/config``中添加
```
Host github.com       
  User git
  ProxyCommand nc -x localhost:8080 %h %p
```
##### 使用XX-net:
XX-net尚不支持port 22，请raise issue
