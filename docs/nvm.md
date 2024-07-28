## NVM

为了方便管理多个 Node.js 版本，我们通过会使用 NVM。
下面展示了在Linux上如何利用`curl`工具帮助下载和安装 `NVM` 与 `Node.js v20.16.0(LTS)`。

```bash
# installs nvm (Node Version Manager)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash

# download and install Node.js (you may need to restart the terminal)
nvm install 20

# verifies the right Node.js version is in the environment
node -v # should print `v20.16.0`

# verifies the right npm version is in the environment
npm -v # should print `10.8.1`
```