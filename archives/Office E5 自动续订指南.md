# Office E5 自动续订指南

在使用 Office E5 订阅时，试用期仅为三个月。若希望继续使用，需在试用期结束前保持一定的开发活动，即频繁调用微软软件的 API。本文将介绍如何通过此方法进行续订。

**重要提示：此方法不能保证续订成功，不能保证续订成功，不能保证续订成功**

## 参考资源

1. [黑幕](https://blog.432100.xyz/index.php/archives/50/)：此方法需要服务器支持，成本较高，但值得借鉴。
2. [AutoApiSecret](https://github.com/wangziyingwen/AutoApiSecret)：此项目参考了上述资源，并利用 GitHub Action 实现，无需服务器，降低了成本。
3. [视频教程](https://arvin-hehe.github.io/office-e5-automatic-subscription.html#video)：由 AutoApiSecret 作者制作，适合不喜欢阅读文字的用户。

## 具体操作步骤

**假设您已拥有 Office E5 订阅。**

### 1. 注册 Microsoft Azure 应用

- **步骤一**：使用 E5 管理员账号登录 [Microsoft Azure](https://portal.azure.com/#home) 主页。
- **步骤二**：点击 `管理Azure Active Directory`，跳转至相关页面。
- **步骤三**：在左侧目录中找到 `应用注册`，点击进入。
- **步骤四**：点击 `新注册`，填写应用信息：
  - 名称：随意填写，建议具有辨识度。
  - 支持账户类型：选择 `任何组织目录（任何Azure AD目录-多租户）中的账户`。
  - 重定向 URI：选择 `Web`，并填入 `http://localhost:53682/`，点击注册。

**注册完成后，进入应用概述页面，复制并保存 `应用程序（客户端）ID`。**

- **步骤五**：点击左侧 `API 权限`，依次点击 `添加权限 -> Microsoft Graph -> 委托的权限`，勾选以下权限：
  - `Files.Read.All`、`Files.ReadWrite.All`
  - `Sites.Read.All`、`Sites.ReadWrite.All`
  - `User.Read.All`、`User.ReadWrite.All`
  - `Directory.Read.All`、`Directory.ReadWrite.All`
  - `Mail.Read`、`Mail.ReadWrite`
  - `MailboxSettings.Read`、`MailboxSettings.ReadWrite`

**勾选所有权限后，点击 `代表 XXX 授予管理员同意`。**

- **步骤六**：点击左侧 `证书和密码`，点击 `新客户端密码`，填写说明并选择年限，点击添加。保存生成的客户端密码值，此为应用秘钥。

### 2. 配置 rclone

- **步骤一**：[下载 rclone](https://downloads.rclone.org/v1.52.3/rclone-v1.52.3-windows-amd64.zip)，解压后不要双击安装。
- **步骤二**：在解压目录中，`shift + 鼠标右键`，打开 PowerShell 窗口，执行 `start cmd` 打开命令提示符。
- **步骤三**：执行以下代码：
  bash
  rclone authorize "onedrive" "应用ID" "应用秘钥"
  
  **将双引号内的内容替换为之前保存的 ID 和秘钥。**

- **步骤四**：浏览器弹出登录界面，登录 E5 账号，显示 `Success` 表示 refresh token 生成成功。
- **步骤五**：回到命令提示符界面，复制 `refresh_token` 的值（需使用 JSON 格式化工具以便查找），引号不要复制。

### 3. GitHub Action 设置

- **步骤一**：[Fork AutoApiSecret 项目](https://github.com/wangziyingwen/AutoApiSecret) 到您的 GitHub 账号。
- **步骤二**：编辑项目中的 `1.txt`，删除原有内容，粘贴您的 `refresh token`，确保结尾无空格或空行。
- **步骤三**：点击 `Settings -> Secrets -> new secret`，新建两个 secret：`CONFIG_ID` 和 `CONFIG_KEY`：
  - `CONFIG_ID`
    bash
    id=r'您的应用ID'
    
  - `CONFIG_KEY`
    bash
    secret=r'您的应用秘钥'
    
- **步骤四**：点击右上角头像，进入个人设置，选择 `Developer settings -> Personal access tokens -> Generate new token`，设置名称为 `GITHUB_TOKEN`，勾选 `repo`、`admin:repo_hook`、`workflow`，点击 `Generate token`。
- **步骤五**：点击 `Actions`，同意协议，刷新页面后点击 `star`，观察是否启动。若显示黄色转圈表示正在启动，绿色对勾表示已启动。
- **步骤六**：启动后，点击 `build`，展开 `Test Api` 查看执行情况。第二天也可查看执行记录。

## 视频教程

以下是 Auto API 作者录制的视频教程，也可参考操作：

<iframe src="https://player.bilibili.com/player.html?aid=95688306&amp;bvid=BV1mE411V74B&amp;cid=163358877&amp;page=1&amp;high_quality=1&amp;danmaku=0"></iframe>

👉 [WildCard | 一分钟注册，轻松订阅海外线上服务](https://bbtdd.com/WildCard)