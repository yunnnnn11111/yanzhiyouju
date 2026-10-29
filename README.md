# 言之有据 · 云端部署版

版本日期：2026-10-29。Python 后端与原生网页前端，支持网页提取与模型 API 两种模式。

## 本仓库文件结构

应用源码保存在 `app-source.zip`；Render 构建时使用 Python 标准库解压到仓库根目录，再安装 Gunicorn。`render.yaml` 位于仓库根目录。源码归档不包含个人配置、运行记录或日志。后续修改应用后需要重新打包源码归档。

## 部署状态

现有应用地址：[打开言之有据工作台](https://yanzhiyouju.onrender.com/)。本地修改需要重新打包上传并完成 Render 部署后才会生效。

## API 工作台

在右上角“API 与数据源设置”选择 API 模式，填写 Base URL、模型名称与自己的 API Key。默认使用 DeepSeek 的 `https://api.deepseek.com`、`deepseek-flash`；也可配置支持 Chat Completions 和 JSON 输出的兼容服务。默认参数核对来源：https://api-docs.deepseek.com/ 与 https://api-docs.deepseek.com/api/create-chat-completion （2026-10-29）。

- “保存设置”不调用模型；“保存并测试”发送一个最小模型请求，测试和后续生成、审查可能产生服务商费用。
- API 模式的主张、三种文案和通道 B 使用真实模型响应；失败会明确提示，不回退固定样例冒充成功。证据门禁和通道 A 本地规则保持执行。
- 请求会向所填服务发送产品档案、选中主张或待审文本。API 不会自动抓取网页；网页提取仍可单独选择。
- API Key 仅存当前访客的服务器内存，最长两小时；服务重启或容量清理也会失效。密钥不写配置文件、台账、导出、浏览器存储或仓库。“清除密钥”会切回网页模式。
- Base URL 仅支持公网 HTTPS 443，拒绝内网地址，不跟随携带密钥的重定向。
- 示例档案与证据勾选不代表产品获得真实认证；模型输出必须人工核验。

## Render 免费部署

1. 将本目录里的应用文件上传到自己的 GitHub 仓库；本目录的 `render.yaml` 必须位于仓库根目录。建议使用私有仓库，并仅将这个仓库授权给 Render。
2. 在 Render 中新建 Blueprint，选择该仓库。`render.yaml` 已指定 Python、Free 实例和启动命令，并自动生成 `SESSION_SECRET`。提交前确认显示为 Free。
3. 等待服务状态为 Live，打开平台实际分配的 HTTPS 地址。检查 `/api/health` 后，再从网页执行一次完整的生成、审查和导出。
4. 将验证成功的应用地址发给朋友。Render 注册地址、GitHub 仓库地址都不是应用访问地址。

若使用 Web Service 手动创建，设置如下：

| 配置 | 值 |
| --- | --- |
| Runtime | Python |
| Instance Type | Free |
| Build Command | `python -m zipfile -e app-source.zip . && pip install -r requirements.txt` |
| Start Command | `gunicorn cloud_app:application --bind 0.0.0.0:$PORT --workers 1 --threads 4 --timeout 60 --access-logfile -` |
| Health Check Path | `/api/health` |
| PYTHON_VERSION | `3.12.10` |
| SESSION_SECRET | 通过平台生成的独立随机密钥；不要写进仓库或聊天 |

必须保持 `--workers 1`：此版本使用单进程锁保护各访客的 JSON 台账。不要使用 `python app.py` 作为公网服务入口。

## 访客记录

- 每个浏览器通过签名 Cookie 区分访客，配置和台账独立；没有账户登录或跨设备同步。
- 清除 Cookie、更换浏览器或更换签名密钥后，原访客记录不会自动恢复。
- 免费实例闲置会休眠，再次访问需等待启动；休眠、重启或重新部署可能丢失本地记录。请及时导出 Markdown 或 JSON。
- 固定网址不代表永不休眠或永久免费。免费配额、服务条件及可用性以 Render 当前规则为准：https://render.com/docs/free

## 网页提取范围

- 默认来源是烟台苹果标准资料页，不是实时商品销售页。当前产品档案为烟台苹果示例。
- 支持无需登录、可直接读取正文的公开 HTTP/HTTPS 页面；不处理验证码、登录限制或仅动态加载的电商页面。
- 生成文案仍结合已有产品档案与本地规则，并非所有字段都会从任意商品页自动识别。
- 网页信息仅为待核验线索，不能作为产品批次检测报告、认证证书或真实经营承诺。
- 网页模式下，历史 v1/v2 作为演示样例保留，v3 使用网页提取，模型通道 B 不执行。API 模式下调用所配置模型，失败会显示错误。

## 发布文件范围

仅发布 `app.py`、`cloud_app.py`、`core/`、`web/`、`data/` 与部署配置、此说明。

不要上传 `runtime_data/`、`config.local.json`、`.env`、个人台账、日志、测试目录或便携版 Python 运行时。仓库已提供 `.gitignore`；网页手动上传文件时也必须自行排除这些内容。

## 本地检查

使用 Python 3.12 执行 `python cloud_app.py`，在本机打开 `http://127.0.0.1:8098`。这个检查地址不能给其他人当公网网址。

离线验证已检查请求格式、访客隔离、签名 Cookie、配置、证据门禁、内容、审查和导出。离线验证使用明确的网页测试替身，不代表实际公网抓取或 Render 部署已经通过；最终仍需上线验证。
