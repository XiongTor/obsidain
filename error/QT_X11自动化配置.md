#### 1. 服务端配置 (仅需执行一次)

确保服务器的 SSH 服务允许 X11 转发。编辑 `/etc/ssh/sshd_config`：

Bash

```
# 确保以下两行没有被注释且为 yes
X11Forwarding yes
X11UseLocalhost yes
```

修改后重启 SSH 服务：`sudo systemctl restart sshd`。

#### 2. 客户端登录方式 (关键)

用户登录时，不需要手动改 `/etc/profile`。只需在登录命令中加入 `-X` 或 `-Y` 参数：
Bash

```
ssh -Y username@server_ip
```

- **-X**: 开启 X11 转发。
    
- **-Y**: 开启受信任的 X11 转发（处理复杂的 GUI 渲染如 Qt/ETE3 时更稳定）。
    
如果是MobaXterm，请检查会话设置（Session Settings）中的 "X11-Forwarding" 复选框是否勾选
#### 3. 移除旧的硬编码配置 (必须)

**请务必删除**你在 `/etc/profile` 或 `~/.bashrc` 中添加的 `export DISPLAY=10.110.3.26:0.0`。

- **原因**：SSH 转发会自动生成一个类似于 `localhost:10.0` 的变量。如果你在脚本里强行覆盖它，SSH 的自动隧道就会失效，导致你依然连不上。

可以通过运行下列命令来检查是否正常：
```
env | grep DISPLAY
```

- **正确的情况**：应该看到类似 `DISPLAY=localhost:10.0`。
    
- **错误的情况**：如果你看到的是你之前手动设置的 `10.110.3.26:0.0`，**请立即注销该行**。手动指定的 IP 会强制走不加密的物理网络，而大多数防火墙会拦截这种流量。