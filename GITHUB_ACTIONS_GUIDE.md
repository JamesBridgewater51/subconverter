# Subconverter GitHub Actions 使用指南

本文档说明如何使用 GitHub Actions 自动运行 subconverter 并将结果上传到 Gist。

## 前置要求

### 1. 创建 GitHub Personal Access Token

1. 访问 [GitHub Settings → Developer settings → Personal access tokens → Tokens (classic)](https://github.com/settings/tokens/new)
2. 创建一个新的 token，选择 **gist** 权限
3. 复制生成的 token（只会显示一次）

### 2. 添加 GitHub Secrets

在你的仓库中添加以下 Secret：

1. 进入仓库的 **Settings → Secrets and variables → Actions**
2. 点击 **New repository secret**
3. 添加：
   - **Name**: `GIST_TOKEN`
   - **Value**: 你在上一步创建的 Personal Access Token

### 3. 配置定时任务的默认参数（可选）

如果你想使用定时任务自动运行，需要添加以下 **Repository Variables**：

在 **Settings → Secrets and variables → Actions → Variables** 中添加：

- `GIST_POOL_URL`: 你的单个订阅池 Gist URL
- `AIRPORT_URLS`: 机场订阅链接（每行一个）
- `TARGET`: 目标格式（默认：`clash`）
- `UPLOAD_FILENAME`: 上传的文件名（默认：`subconverter_config.yaml`）

## 使用方法

### 方法 1: 手动触发

1. 进入仓库的 **Actions** 标签
2. 选择 **Subconverter Gist Upload** workflow
3. 点击 **Run workflow**
4. 填写参数：
   - **Single subscription pool Gist URL**: 你的订阅池 URL
   - **Airport subscription URLs**: 机场订阅链接（每行一个）
   - **Target format**: 目标格式（如 `clash`、`surge` 等）
   - **Filename for uploaded Gist**: 上传到 Gist 的文件名
5. 点击 **Run workflow** 执行

### 方法 2: 定时自动运行

Workflow 已配置为每天 00:00 UTC（北京时间 08:00）自动运行。

确保你已经在 Repository Variables 中配置了所需的参数（见上文）。

## Workflow 工作流程

1. **Checkout repository**: 检出代码以获取配置文件
2. **Configure Gist token**: 将 GIST_TOKEN 写入 `base/gistconf.ini`
3. **Start Subconverter service**: 使用 Docker 启动 subconverter 服务
4. **Wait for service**: 等待服务就绪
5. **Combine subscription URLs**: 合并订阅池和机场订阅链接
6. **Convert and upload**: 调用 subconverter API 并上传到 Gist
7. **Cleanup**: 清理 Docker 容器

## 支持的目标格式

根据 subconverter 文档，支持以下目标格式：

- `clash` - Clash
- `surge` - Surge（可指定版本：`surge&ver=2/3/4`）
- `quan` - Quantumult
- `quanx` - Quantumult X
- `loon` - Loon
- `ss` - Shadowsocks (SIP002)
- `ssr` - ShadowsocksR
- `v2ray` - V2Ray
- `surfboard` - Surfboard
- 等等

## 故障排查

如果 Workflow 失败：

1. 检查 **Actions** 标签中的日志
2. 确认 `GIST_TOKEN` 已正确配置
3. 确认订阅 URL 是可访问的
4. 检查 Docker 容器日志（会在失败时自动输出）

## 注意事项

- 订阅 URL 会使用 `|` 分隔符合并
- 所有 URL 会自动进行 URL 编码
- Gist 上传使用 subconverter 内置的 `upload=true` 参数
- Docker 容器会自动清理，不会占用资源
