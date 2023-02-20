## Reids 的安装与配置
升级本地的 apt package cache:
```
$ sudo apt update
```
安装 Redis:
```
$ sudo apt install redis-server
```

使用编辑器(vim or nano) 打开 *redis.conf* :
```
$ sudo vim /etc/redis/redis.conf
```
找到 `supervised` 指令并将其值修改为 `systemd`.
![[Pasted image 20230220150649.png]]
保存文件并退出编辑器，重启 Redis 服务让修改的指令生效:
```
sudo systemctl restart redis.service
```

## 测试 Redis
检查 Redis 服务是否正常运行
```
sudo systemctl status redis
```
若 Redis 服务正常运行，会输出以下类似的消息：
![[Pasted image 20230220151858.png]]
当看到 Redis 的状态是 `active (running)` 及 `enabled`, 说明该服务会随操作系统而启动。

使用 redis-cli 测试 Redis 的功能是否正常：
```
$ redis-cli
```


使用 `ping` 测试 客户端与Redis 服务的连接.
![[Pasted image 20230220105334.png]]

检查set 指令是否正常：
![[Pasted image 20230220110149.png]]

上述检查确认无误后，退出 `redis-cli`:
![[Pasted image 20230220112416.png]]

检查 Redis 在停止服务或重启的情况下能否正常保存数据.
重新启动 Redis 实例：
```
$ sudo systemctl restart redis
```
再次用 `redis-cli` 连接到服务:
```
$ redis-cli
```
用 `get` 指令测试取值:
```
127.0.0.1：6379> get test
```
![[Pasted image 20230220114741.png]]
完成测试后，可退出 `redis-cli`:
```
127.0.0.1：6379> exit
```


## 绑定到 localhost (可选)
默认情况下，Redis 只允许本地访问，若由于某些情况修改了配置，可参考以下方法重新绑定为本地访问。

打开 *redis.conf*:
```
sudo vim /etc/redis/redis.conf
```

定位到下面的一行并取消其注释：
```
bind 127.0.0.1 ::1
```
完成后保存文件并退出。

重新启动 Redis 服务，确保修改生效:
```
$ sudo systemctl restart redis
```

运行`netstat` 检查修改是否生效:
```
$ sudo netstat -lnp | grep redis
```
![[Pasted image 20230220134544.png]]
以上输出说明 redis-server 已经被绑定到 `localhost(127.0.0.1)`, 若有其他的 IP 出现，需要再检查修改是否成功，并重新启动 redis 服务。

*注意：若系统中没有  netstat, 可使用以下指令安装*
```
sudo apt install net-tools
```


## 为 Redis 配置访问密码 (可选)
打开 *redis.conf*:
```
$ sudo vim /etc/redis/redis.conf
```

找到 *SECURITY* 部分或 `requirepass` 指令：
```
...
# requirepass foobared
...
```
移走 `#`，把 `foobared` 改为一个安全的密码。

可使用 `openssl` 命令生成一个随机密码:
```
openssl rand 60 | openssl base64 -A
```
![[Pasted image 20230220143121.png]]
把新生成的随机密码复制粘贴到 `requirepss` 后替换 `foobared`.
*/etc/redis/redis.conf*
![[Pasted image 20230220143358.png]]

设置密码后，重启 Redis 服务:
```
$ sudo systemctl restart redis.service
```

检查密码是否正常：
![[Pasted image 20230220144435.png]]

## 安装 fastify-redis
在项目根目录下用命令 `npm ls fastify` 查看当前 fastify的版本号：
```
$npm ls fastify
```

![[Pasted image 20230220164211.png]]
当前使用的版本是 `3.25.3` ,发布于 29 Dec, 2021， 所以需安装符合当前 Fastify 版本的 Fastify-redis.
![[Pasted image 20230220171433.png]]
出自 https://security.snyk.io/package/npm/fastify

故选择 `fastify-redis` 的 `V4.4.4` 版本。
在 InstaBIS 中安装 `fastify-redis`:
```
$ npm i fastify-redis@4.4.0
```


## 参考
[Getting started with Redis](https://redis.io/docs/getting-started/)
[How To Install and Secure Redis on Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-secure-redis-on-ubuntu-20-04)
[How to Install Redis on Ubuntu 20.04 / 18.04](https://phoenixnap.com/kb/install-redis-on-ubuntu-20-04)
[fastify-redis](https://github.com/fastify/fastify-redis)