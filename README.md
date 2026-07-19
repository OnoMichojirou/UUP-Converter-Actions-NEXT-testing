# 🖥️ UUP Dump ISO 在线构建器

基于 GitHub Actions 的 Windows ISO 自动化构建工具。上传从 [UUP dump](https://uupdump.net) 下载的转换包，一键生成 Windows ISO 镜像。

---

## ✨ 功能特点

- 🚀 **一键构建** — 上传 ZIP 包后，点击按钮即可自动构建
- 🔄 **全自动流程** — 自动下载 UUP 文件、下载转换器、生成 ISO
- 📦 **Artifacts 下载** — 构建完成后直接在 Actions 页面下载，无需 Release
- 🎯 **支持虚拟版本** — 通过 `ConvertConfig.ini` 配置，可生成 Enterprise、Education、IoT Enterprise 等版本
- 🔒 **无需 Token** — 不依赖 `GITHUB_TOKEN` 写权限

---

## 📋 前置要求

1. 一个 **GitHub 账号**
2. 从 [uupdump.net](https://uupdump.net) 下载的 **UUP 转换包**（`.zip` 格式）

---

## 🚀 使用步骤

### 第一步：获取 UUP 转换包

1. 打开 [uupdump.net](https://uupdump.net)
2. 搜索并选择你要构建的 Windows 版本（如 Windows 11 24H2）
3. 选择 **语言** 和 **架构**
4. 在"虚拟升级版本"页面勾选你需要的版本（如企业版、IoT 企业版等）
5. 点击 **"创建下载包"** 按钮
6. 下载得到的 `.zip` 文件

### 第二步：上传至仓库

1. Fork 本仓库或创建一个新仓库
2. 将下载的 `.zip` 文件**上传到仓库根目录**（不要解压）
3. Commit 并 Push

```
你的仓库/
├── .github/
│   └── workflows/
│       └── build-iso.yml    ← GitHub Actions 配置
├── 22000.3260_amd64_zh-cn_multi_xxxx_convert_virtual.zip   ← 你的 UUP 包
└── README.md
```

### 第三步：触发构建

1. 进入仓库的 **Actions** 标签页
2. 选择左侧的 **Build Windows ISO**
3. 点击右侧的 **Run workflow** → **Run workflow**
4. 等待构建完成（约 40~90 分钟）

### 第四步：下载 ISO

1. 构建完成后，点击进入该次运行详情页
2. 滚动到页面底部的 **Artifacts** 区域
3. 点击 **windows-iso** 下载生成的 ISO 文件
4. 解压下载的 ZIP，即可得到 `.iso` 镜像

> ⚠️ Artifacts 默认保留 **7 天**，请及时下载。

---

## 📁 文件说明

| 文件 | 说明 |
|------|------|
| `uup_download_windows.cmd` | 下载 UUP 文件的脚本（包内自带） |
| `ConvertConfig.ini` | 转换配置，控制压缩格式、虚拟版本等（包内自带） |
| `files/` | UUP 文件清单（包内自带） |
| `*.iso` | 构建完成后生成的 Windows 安装镜像 |
| `*.sha256.txt` | ISO 文件的 SHA256 校验值 |

---

## ⚙️ ConvertConfig.ini 常用配置

你的 UUP 包中的 `ConvertConfig.ini` 已经包含了配置，如需调整可修改后重新上传：

```ini
[Options]
; 压缩格式: 1=WIM, 2=ESD（ESD 体积更小）
Compress=2

; 集成最新累积更新
AddUpdates=1

; 集成 .NET Framework 3.5
NetFx3=1

; 清理组件以减小体积
CleanupImage=1

; 生成 ISO 文件
StartISO=1
```

---

## ⚠️ 注意事项

| 项目 | 说明 |
|------|------|
| **磁盘空间** | GitHub Actions Windows runner 提供约 20GB 可用空间，一般足够构建 |
| **构建时间** | 下载 UUP 文件约 15~30 分钟，转换约 20~40 分钟，总计约 1 小时 |
| **Artifacts 限制** | 单个文件最大 5GB，Windows 11 ISO 通常 4~6GB，超出会构建失败 |
| **网络稳定性** | 从 Microsoft Update CDN 下载，国内网络可能较慢 |
| **构建失败** | 如果提示 `EMPTY_FILELIST`，说明该构建已被微软下架，请重新去 uupdump.net 下载新包 |

---

## 🔧 常见问题

**Q: 可以一次上传多个 ZIP 包吗？**
> 不可以。Workflow 会自动查找根目录下的第一个 `.zip` 文件，多个包会混淆。

**Q: 支持 Windows 10 吗？**
> 支持。只要 uupdump.net 上能下载到的版本都支持，包括 Windows 10/11 的各个通道（Release、Beta、Dev、Canary）。

**Q: 生成的 ISO 是正版吗？**
> 这是从微软官方 UUP 服务器下载的原版文件转换而来，**未做任何修改**。激活需要自行购买正版密钥。

**Q: 如何生成 LTSC 版本？**
> 在 uupdump.net 搜索时直接搜 "LTSC"，或在虚拟版本页面勾选 IoT Enterprise LTSC。

---

## 📜 免责声明

本项目仅用于学习和研究目的。使用 UUP dump 生成的镜像应遵守 Microsoft 软件许可条款。作者不对因使用本工具产生的任何后果负责。

---

## 🙏 致谢

- [UUP dump](https://uupdump.net) — 提供 UUP 文件解析和下载服务
- [abbodi1406/uup-converter-wimlib](https://github.com/abbodi1406/BatUtil) — 提供 UUP 转换工具
