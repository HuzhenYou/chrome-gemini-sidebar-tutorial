# chrome 开启 gemini 侧边栏教程

### 1. 修改 Chrome 本地配置文件（最关键）

Chrome 会在本地记录你的“永久性国家码”。即使开了代理，这个值如果锁定在 `cn`，功能也不会开启。

1. **完全退出 Chrome**：确保快捷键 `Command + Q` 彻底关闭。
2. **打开终端 (Terminal)**：按下 `Command + Space` 输入 `Terminal` 并回车。
3. **运行备份命令**（保险起见）：
```bash
cp ~/Library/Application\ Support/Google/Chrome/Local\ State ~/Library/Application\ Support/Google/Chrome/Local\ State.bak

```


4. **运行修改命令**（将国家码强制改为 `us`）：
```bash
sed -i '' 's/"variations_country":"cn"/"variations_country":"us"/g' ~/Library/Application\ Support/Google/Chrome/Local\ State
sed -i '' 's/"variations_permanent_consistency_country":\[[^]]*\]/"variations_permanent_consistency_country":["us"]/g' ~/Library/Application\ Support/Google/Chrome/Local\ State

```


---

### 2. 调整系统与浏览器基础设置

1. **macOS 系统区域**：
* 进入 **系统设置 (System Settings) > 通用 (General) > 语言与地区 (Language & Region)**。
* 将 **地区 (Region)** 修改为 **美国 (United States)**。


2. **Chrome 语言**：
* 打开 Chrome，进入 `chrome://settings/languages`。
* 确保 **英语（美国）English (United States)** 位于列表最顶端，并勾选“以这种语言显示 Google Chrome”。


3. **Google 账号设置**：
* 访问 [Google 账号管理](https://myaccount.google.com/personal-info)。
* 确保你的账号年龄满 18 岁，且在“地址”或“付款方式”中没有绑定大陆的账单地址（最好清除或更换为美区 Profile）。



---

### 3. 开启 AI 实验功能

在完成上述步骤并保持**美国代理**的情况下重启 Chrome：

1. 在地址栏输入 `chrome://settings/ai`。
2. 你应该能看到 **“AI Innovations” (AI 创新)** 或 **“Gemini in Chrome”** 的开关。
3. 将其打开，随后在浏览器右上角（地址栏右侧）会出现 Gemini 的星形图标。

---

### 4. 进阶：使用命令行参数启动 (如果上述无效)

如果侧边栏仍不出现，可以尝试通过终端带参数强制启动 Chrome，绕过部分地理限制：

```bash
open -a "Google Chrome" --args --force-fieldtrials="GeminiSettings/Enabled/" --lang=en-US

```

### 💡 避坑指南

* **不要开启无痕模式**：目前 Gemini 集成在无痕模式下默认不可用。
* **全局代理**：建议开启 VPN 的“增强模式”或“全局模式”，确保 `google.com` 相关的子域名（如 `static.googleusercontent.com`）不走直连，否则 Chrome 可能会实时更新你的位置。


这一步我实现了让gemini侧边栏看起，这说明你的实验性标志位（Flags）已经生效，但服务器端拦截了，即 Google 通过你的 IP 地址、账号区域或浏览器持久化缓存识别到了你在受限地区。
<img width="1920" height="1045" alt="Pasted image 20260205135716" src="https://github.com/user-attachments/assets/067b11a3-cae2-4b7b-abe6-fece50978f1b" />


接续设置：


### 1. 修改 `Local State` 文件（强制伪装区域码）

即使界面出来了，Chrome 内部还会记录一个永久的国家码。手动将其修改为 `us` 是最有效的方法。

1. **完全退出 Chrome**：按下 `Command + Q` 彻底关闭浏览器。
2. **打开终端 (Terminal)**，复制并执行以下命令：
```bash
# 进入配置文件夹
cd ~/Library/Application\ Support/Google/Chrome/

# 备份原文件（以防万一）
cp "Local State" "Local State.bak"

# 修改国家码为 us (包含两处关键位置)
sed -i '' 's/"variations_country":"cn"/"variations_country":"us"/g' "Local State"
sed -i '' 's/"variations_permanent_consistency_country":\[[^]]*\]/"variations_permanent_consistency_country":["us"]/g' "Local State"

```


*注意：如果你的命令返回错误，请手动用“文本编辑”打开 `Local State` 文件，搜索 `variations_country` 并将后面的 `cn` 改为 `us`。*

### 2. 账号区域与语言一致性

Google 会交叉比对你的账号信息：

* **浏览器语言**：进入 `chrome://settings/languages`，确保 **English (United States)** 位于列表第一位，并设置为“以此语言显示 Chrome”。
* **Google 账号设置**：访问 [Google 个人信息页](https://myaccount.google.com/personal-info)，确保“语言”设置为英语。同时检查你的 [Google 付款个人资料](https://pay.google.com/gp/w/home/settings)，如果绑定的是中国大陆地址，可能会导致拦截。

### 3. 检查代理泄露（IPv6 与 WebRTC）

侧边栏报错通常是因为你的真实 IP 通过其他协议泄露给了 Google：

* **禁用 IPv6**：在 macOS **系统设置 > 网络 > 已连接的 Wi-Fi > 详细信息 > TCP/IP** 中，将“配置 IPv6”改为“**仅本地链接**”或“关闭”。
* **WebRTC 泄露**：安装 **WebRTC Control** 扩展，确保你的真实 IP 不会绕过代理。
* **代理模式**：确保你的代理软件开启了“**全局模式**”或“**增强模式**”，仅“分流模式”往往会漏掉 Chrome 内部的某些地理位置请求。

### 4. 清理浏览器缓存

完成上述修改后，必须清理一次缓存，否则旧的定位标记会一直存在：

1. 打开 `chrome://settings/clearBrowserData`。
2. 选择“**Cookie 及其他网站数据**”和“**缓存的图片和文件**”，时间范围选“所有时间”，点击清理。
3. **重启 Chrome**。



大功告成！！

