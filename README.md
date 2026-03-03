# 解决GitHub SSH 22端口超时：切换至443端口（Windows版）
当向GitHub推送代码时遇到 `ssh: connect to host xx.xx.xx.xx port 22: Connection timed out` 错误，说明22端口被网络/防火墙限制，可通过修改SSH配置使用443端口解决，以下是Windows系统的详细操作步骤。

## 一、修改SSH配置文件（指定443端口）
### 1. 定位.ssh文件夹
Windows系统中SSH配置文件存放在用户目录下的.ssh文件夹中：
1. 按下 `Win + E` 打开文件资源管理器；
2. 在地址栏输入 `%USERPROFILE%\.ssh` 并回车，直接进入目标文件夹（可看到id_rsa、id_rsa.pub、known_hosts文件）。

### 2. 创建/编辑config文件
- **无config文件时**：右键空白处 → 新建 → 文本文档 → 重命名为`config`（需删除.txt后缀，若看不到后缀，在“查看”选项卡勾选“文件扩展名”）；
- **已有config文件时**：右键文件选择“编辑”直接打开。

### 3. 添加443端口配置
在config文件中粘贴以下内容并保存：
```
# GitHub SSH 443端口配置
Host github.com
Hostname ssh.github.com
Port 443
User git
IdentityFile ~/.ssh/id_rsa  # 指向本地私钥文件，默认路径无需修改
```

## 二、测试修改后的SSH连接
1. 按下 `Win + R`，输入`cmd`打开命令提示符（或PowerShell）；
2. 执行测试命令：
   ```bash
   ssh -T git@github.com
   ```
3. 验证结果：
   - 成功：提示 `Hi 你的GitHub用户名! You've successfully authenticated, but GitHub does not provide shell access.`（首次连接需输入`yes`信任主机）；
   - 失败：检查config文件格式（无多余空格、端口为443）或网络是否放行443端口。

## 三、验证代码Push操作
1. 命令行切换至本地Git仓库目录：
   ```bash
   cd 你的仓库路径  # 示例：cd D:\Projects\my-github-project
   ```
2. 执行Push命令（替换`分支名`为实际分支，如main）：
   ```bash
   git push origin 分支名  # 示例：git push origin main
   ```
3. 结果：若之前因22端口超时失败，此时可正常推送代码至GitHub。

## 总结
1. Windows核心操作是修改`%USERPROFILE%\.ssh\config`文件，指定GitHub的SSH连接端口为443；
2. 测试连接是验证配置有效性的关键步骤，需确保命令返回成功认证提示；
3. 若仍失败，优先排查防火墙/代理是否限制443端口，或GitHub SSH密钥是否已正确配置。