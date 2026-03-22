## 主要是如何通过公钥密钥来实现无需密码验证登录服务器
# **SSH key + ssh-agent 配置流程**

---

## **1️⃣ 生成 SSH key（如果已经有可以跳过）**

在 Git Bash 输入：

`ssh-keygen -t ed25519 -C "xiongtao@rosa-server"`

- 保存位置：直接回车（默认 `~/.ssh/id_ed25519`）
    
- passphrase：**推荐输入一个密码**，增强安全（可以回车空密码测试）
    

执行完后，会生成两个文件：

`~/.ssh/id_ed25519      # 私钥 ~/.ssh/id_ed25519.pub  # 公钥`

---

## **2️⃣ 启动 ssh-agent 并加载私钥**

在 Git Bash：

`eval "$(ssh-agent -s)" ssh-add ~/.ssh/id_ed25519`

- 如果你设置了 passphrase，会提示输入一次
    
- 成功后，用下面命令验证：
    

`ssh-add -l`

你会看到类似：

`256 SHA256:xxxx id_ed25519 (ED25519)`

✅ 表示私钥已加载

---

## **3️⃣ 把公钥上传到服务器**

在本机：

`cat ~/.ssh/id_ed25519.pub`

复制整行内容，然后在服务器上执行：


```
ssh xiongtao@122.205.95.19   # 如果还用密码登录
mkdir -p ~/.ssh chmod 700 ~/.ssh 
nano ~/.ssh/authorized_keys    # 粘贴刚才的公钥
chmod 600 ~/.ssh/authorized_keys
```

> 注意权限，`700 ~/.ssh` 和 `600 authorized_keys` 是关键，否则 SSH 会拒绝

---

## **4️⃣ 测试 SSH 登录**

在本机 Git Bash：

`ssh xiongtao@122.205.95.19`

- 正确输出：直接登录，不问密码
    
- 错误输出：回到第 3 步检查公钥和权限
    

---

## **5️⃣ 配置 Git remote 推送两个地址**

假设你现在 `origin` 只指向 GitHub：

`git remote -v origin  git@github.com:XiongTor/Rosa_family.git (fetch) origin  git@github.com:XiongTor/Rosa_family.git (push)`

### 增加服务器 push URL

`git remote set-url --add --push origin ssh://xiongtao@122.205.95.19//home/miaosun/share/xiongtao/xiong_github/Rosa_family.git`

验证：

`git remote -v`

你应该看到：

`origin  git@github.com:XiongTor/Rosa_family.git (fetch) origin  git@github.com:XiongTor/Rosa_family.git (push) origin  ssh://xiongtao@122.205.95.19//home/miaosun/share/xiongtao/xiong_github/Rosa_family.git (push)`

✅ 这样你就完成了“双 push URL”的安全配置

---

## **6️⃣ 测试一次 push**

`git push -u origin main`

你应该会看到：

`To github.com:XiongTor/Rosa_family.git    xxxx..yyyy  main -> main To 122.205.95.19:/home/.../Rosa_family.git    xxxx..yyyy  main -> main`

- **GitHub** 同步 ✅
    
- **服务器** 同步 ✅
    
- **不再提示密码，不再报 500 / askpass**
    

---

## **7️⃣ 小提示**

- VS Code 现在可以直接 `Push`，一次操作两边同步
    
- ssh-agent 在重启后可能需要重新 `ssh-add`，可以写入 Git Bash 配置自动加载

-  私钥位置一般在个人电脑的：C:\Users\<你的用户名>\.ssh\id_ed25519，配置成功后仅在配置公钥私钥的个人电脑上有用，更管电脑后需要重新配置

- 另外，如果配置完成后随意挪动本地公钥私钥文件的位置，会导致登录时本地电脑无法找到私钥导致登录失败，此时需要重新配置路径



## 如果你想服务器直接作为可读可操作的仓库：

```
# 登录服务器 
ssh 用户名@服务器IP  
# 进入目标目录 
cd /home/miaosun/share/xiongtao/xiong_github/ mkdir Rosa_family cd Rosa_family  
# 初始化非裸仓库 
git init  
# 设置默认分支 
git checkout -b main  # 或 master  
# 允许 push 到这个仓库（需要设置 receive.denyCurrentBranch） 
git config receive.denyCurrentBranch updateInstead
```