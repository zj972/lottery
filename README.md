# lottery

> A Lottery Systeam

抽奖系统

index文件在dist里，需要配置nginx和host指向，然后打开lottery.oa.com就可以啦，参考如下：

nginx.conf

```
server {
        listen       80;
        server_name  lottery.oa.com;

        location / {
            root   E:\SarboYang/lottery/dist;
            index  index.html index.htm;
            try_files $uri $uri/ /index.html;
        }
    }
}
```

host

```
127.0.0.1 lottery.oa.com
```
