---
title: ssh-agent, ssh agent, ssh forward
author: "-"
date: 2022-06-07 18:25:49
url: ssh-agent
categories:
  - Linux
tags:
  - reprint
  - remix
  - SSH
---
## ssh-agent, ssh agent, ssh forward

## ssh agent forward

A > B > C

- 主机 A：运行 ssh-agent，并且已经加载私钥
- 主机 B：跳板机
- 主机 C: 目标机，已经配置好公钥，并且运行 sshd

检查主机 A ssh-agent 是否已经在运行

```bash
ps -ef | grep ssh-agent
```

主机 A ssh-agent 已经加载了密钥

```bash
ssh-add -l
```

主机 A 允许入站连接上的 SSH 代理转发, 将 AllowAgentForwarding 的值设置为 yes，表示允许进行代理转发， openssh 中 AllowAgentForwarding 默认值即为yes，所以，如果配置没有修改过，保持默认即可。

```bash
vim /etc/ssh/sshd_config
    AllowAgentForwarding yes
```

主机 A ssh 配置 ForwardAgent yes

```bash
vim ~/.ssh/config

#content
host *
  ForwardAgent yes
```

主机 B 不需要运行 ssh-agent, 也不需要配置 ssh 的 `ForwardAgent yes`,  
主机 B 在使用 ssh agent forward 连接 主机 C 成功之后，会创建 环境变量 `$SSH_AUTH_SOCK`

### 测试密钥是否可用

从 主机 A ssh 登录到主机 B，然后运行:

```bash
ssh -T git@host-C.com
# 比如 github就相当于是 主机 C
ssh -T git@github.com
```

---

## 临时运行

直接执行的话不能导入环境变量, 只是测试一下 ssh-agent 能否正常运行

```bash
ssh-agent
# 回显是这样的
SSH_AUTH_SOCK=/tmp/ssh-XXXXXXmfJNRj/agent.43061; export SSH_AUTH_SOCK;
SSH_AGENT_PID=43062; export SSH_AGENT_PID;
echo Agent pid 43062;
```

### 导入环境变量

```bash
    eval $(ssh-agent)
```

用 eval 可以把 SSH_AUTH_SOCK 等环境变量临时导入当前 shell

### archlinux 启动后自动启用 ssh-agent

Start ssh-agent with systemd user

```bash
vim ~/.config/systemd/user/ssh-agent.service

# content
[Unit]
Description=SSH key agent

[Service]
Type=simple
Environment=SSH_AUTH_SOCK=%t/ssh-agent.socket
ExecStart=/usr/bin/ssh-agent -D -a $SSH_AUTH_SOCK

[Install]
WantedBy=default.target
```

### .pam_environment 没有的话就创建一个新文件

```bash
vim  ~/.pam_environment
SSH_AUTH_SOCK DEFAULT="${XDG_RUNTIME_DIR}/ssh-agent.socket"

```

```bash
# Then enable or start the service.
systemctl --user enable ssh-agent
systemctl --user start ssh-agent
# 检查环境变量 SSH_AUTH_SOCK
env | fgrep SSH_
# 如果看不到SSH_AUTH_SOCK , 重启再试一下 
```

### 查看 ssh agent 进程

```bash
ps -ef | grep ssh-agent
echo "$SSH_AUTH_SOCK"
```

### ssh agent

### 查看 缓存的密钥

```bash
# 查看本地SSH agent 缓存的密钥对应的公钥
ssh-add -L

# 查看缓存的私钥 sha256 值
ssh-add -l

# 删除全部 key
ssh-add -D
```

## kill ssh-agent

```bash
    kill $SSH_AGENT_PID
```

## 把密钥添加到 ssh-agent

```bash
    ssh-add /path/to/private_key
```

### 环境变量

```bash
    echo $SSH_AGENT_PID
    echo $SSH_AUTH_SOCK
```

ssh-agent 是用于管理 SSH private keys 的, 长时间持续运行的守护进程 (daemon) . 唯一目的就是对解密的私钥进行高速缓存.
ssh-add 提示并将用户的使用的私钥添加到由 ssh-agent 维护的列表中. 此后, 当使用公钥连接到远程 SSH 或 SCP 主机时, 不再提示相关信息.

[http://xstarcd.github.io/wiki/shell/fork_exec_source.html](http://xstarcd.github.io/wiki/shell/fork_exec_source.html)

## ssh-agent

```bash
    # vim ~/.bashrc

    if ! pgrep -u "$USER" ssh-agent > /dev/null; then
      ssh-agent -t 1h > "$XDG_RUNTIME_DIR/ssh-agent.env"
    fi
    if [[ ! "$SSH_AUTH_SOCK" ]]; then
        source "$XDG_RUNTIME_DIR/ssh-agent.env" >/dev/null
    fi

    # start the ssh-agent in the background
    eval "$(ssh-agent -s)"
    # Add the SSH key to the ssh-agent
    ssh-add ~/.ssh/id_rsa
```

### 开启 ssh forward

#### 用户级

```bash
vim ~/.ssh/config
# content
Host *
ForwardAgent yes
User root
```

> vim /etc/ssh/ssh_config

```bash
Host *
    ForwardAgent yes
```

修改配置文件之后记得重启 sshd

```bash
    sudo systemctl restart sshd
```

---

[https://www.jianshu.com/p/12de50582e63](https://www.jianshu.com/p/12de50582e63)

ssh 到 node1
  
由 node1 跳板到任意机器。
  
然而失败了。

XiaoleideMacBook-Pro:~ professor$ vi .bashrc
  
alias go-node1='ssh -p 1046 hzlixiaolei@10.165.xxx.xxx'
  
XiaoleideMacBook-Pro:~ professor$ go-node1
  
-bash: go-node1: command not found
  
XiaoleideMacBook-Pro:~ professor$ source .bashrc
  
-bash: SSH_AUTH_SOCK=/var/folders/9x/7zl7w59n55s3wb_wjg7d5zfh0000gn/T//ssh-XpUY3kpYkJnP/agent.9483;: No such file or directory
  
XiaoleideMacBook-Pro:~ professor$ go-node1

The programs included with the Debian GNU/Linux system are free software;
  
the exact distribution terms for each program are described in the
  
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
  
permitted by applicable law.
  
Last login: Mon Feb 6 15:51:21 2017 from 10.165.xx.xx
  
hzlixiaolei@node1:~$go-node2
  
(public key denied)
  
如图所示,我从node1往node2跳的时候出现公钥无效。

解决办法
  
其实这里的问题就在于我没有开ssh-agent.

正确配置ssh-agent forwardi配置即可成功:

2, 配置云主机的SSH-agent forwarding
  
配置步骤同上

3,查看本地SSH agent是否携带本地的ssh key
  
Mac特有的一个问题,要注意
  
On Mac OS X, ssh-agent will "forget" this key, once it gets restarted during reboots. But you can import your SSH keys into Keychain using this command:

ssh-add -L
  
好了,问题解决。

### SSH, SSH agent & SSH agent Forwarding

这里, 必须需要说下这三者的联系。

#### 我们常用的 SSH 工作原理, 通过 publickey access

1. 用户发起连接, 携带者用户名
2. ssh 守护进程 (sshd) 在 Server 上查看 authorized_keys 文件, 基于 publickey 构造一个口令盘问发送给 SSH client, The ssh daemon on the server looks in the user's authorized_keys file, constructs a challenge based on the public key found there, and sends this challenge back to the user's ssh client.
3. SSH client 收到后, 在本地查询 privatekey (默认id_rsa文件), 此时如果有密码, 会要求输入密码。
4. ssh client 通过 privatekey 构造一个响应(私钥签名)。发送给 ssh server。 注意: 这里并不会发送 privatekey 本身。
5. ssh server 验证, 授权成功

### SSH agent 是干嘛的

如果每次我们都 SSH 到某个 server, 我们如果 privatekey 有密码, 如果没有 ssh agent, 每次我们都会需要被告知要输入密码。有了ssh Agent,就不需要了。 因为它负责管理 key。

与上面相比, 唯一的区别在第三步和第四步:
  
根据 privatekey 构造响应的操作有 ssh-agent 来做了。ssh client 没有和 privatekey 有联系。所以后面的访问, 都是 ssh-agent 来管理, 又因为我们之前输入过密码, ssh-agent 仍然记录这个状态, 所以之后就不用再输入密码了。

### ssh agent forwarding

简单来说,agent forwarding 运行一串的 ssh 连接。将 sshd 的口令盘问直接发送到最初始的  ssh client, 而不需要任何中间集群的认证。
  
如果按照我们上面的配置, 配置好了agent forwarding,它是如何工作的:
  
- Step1,基于上面的ssh到server1,用户在server1上开始发起到server2的链接 (这一步和之前一样)
- Step2: server2 的 sshd 查询用户的 authorized_keys 文件, 并像之前一样构造一个口令盘问发回给 server1 的 ssh。下面神奇的事情就发生了: server1 的 ssh 发送给自己的 sshd, 并再一次relay 给我们 pc 的 ssh。
- Step3: 后面的步骤就是 PC 的ssh agent根据privatekey构造key response并串行的发到server2的sshd上。然后完成鉴权。
- Step4: 如果需要在往Server3,4,N,仍然有效。

---

[https://blog.csdn.net/vizts/article/details/47043695](https://blog.csdn.net/vizts/article/details/47043695)
[https://www.fythonfang.com/blog/2017/12/27/ssh-agent-and-ssh-agent-forwarding](https://www.fythonfang.com/blog/2017/12/27/ssh-agent-and-ssh-agent-forwarding)
[https://wiki.archlinux.org/index.php/Systemd/User](https://wiki.archlinux.org/index.php/Systemd/User)
[https://wiki.archlinux.org/index.php/SSH_keys](https://wiki.archlinux.org/index.php/SSH_keys)
[https://www.jianshu.com/p/12de50582e63](https://www.jianshu.com/p/12de50582e63)

## win11 ssh-agent

搜索 服务
启用并启动 openssh authentication agent

## win 10 ssh-agent

```bash
# run in powershell
# 查看 ssh-agent
Get-Service ssh-agent
```

[https://corvo.myseu.cn/2020/10/16/2020-10-16-OpenSSH%E7%B3%BB%E5%88%97(%E6%89%A9%E5%B1%95%E4%B8%89)-%E5%85%B3%E4%BA%8Eforward%20agent%E7%9A%84%E4%BD%BF%E7%94%A8%E4%BB%A5%E5%8F%8A%E8%B0%83%E8%AF%95/](https://corvo.myseu.cn/2020/10/16/2020-10-16-OpenSSH%E7%B3%BB%E5%88%97(%E6%89%A9%E5%B1%95%E4%B8%89)-%E5%85%B3%E4%BA%8Eforward%20agent%E7%9A%84%E4%BD%BF%E7%94%A8%E4%BB%A5%E5%8F%8A%E8%B0%83%E8%AF%95/)

[https://www.zsythink.net/archives/2422](https://www.zsythink.net/archives/2422)
