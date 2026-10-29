# 言之有据 · 云端部署版

版本日期：2026-10-29。Python 后端与原生网页前端，不需要 DeepSeek API Key。

## 本仓库文件结构

应用源码保存在 `app-source.zip`；Render 构建时使用 Python 标准库解压到仓库根目录，再安装 Gunicorn。`render.yaml` 位于仓库根目录。源码归档不包含个人配置、运行记录或日志。后续修改应用后需要重新打包源码归档。

## 部署状态

应用已于 2026-10-29 部署到 Render Free，并完成实际公网 HTTPS 验证。固定访问地址：**https://yanzhiyouju.onrender.com/**。可直接转发给朋友，无需保持个人电脑开机。不要将本机文件路径、localhost、127.0.0.1 或 GitHub 仓库地址当作应用网址。

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
- 历史 v1/v2 作为演示样例保留，v3 使用网页提取。模型审查通道 B 不执行。

## 发布文件范围

本仓库发布文件为 `app-source.zip`、`.gitignore`、`.python-version`、`README.md`、`render.yaml` 和 `requirements.txt`。源码归档包含 `app.py`、`cloud_app.py`、`core/`、`web/`、`data/` 等应用文件；构建时解压，后续修改后需重新打包。

不要上传 `runtime_data/`、`config.local.json`、`.env`、个人台账、日志、测试目录或便携版 Python 运行时。仓库已提供 `.gitignore`；网页手动上传文件时也必须自行排除这些内容。

## 本地检查

使用 Python 3.12，先执行 `python -m zipfile -e app-source.zip .` 解压源码，再执行 `python cloud_app.py`，在本机打开 `http://127.0.0.1:8098`。这个检查地址不能给其他人当公网网址。

2026-10-29 已完成离线检查和实际 Render 公网验证：健康检查、首页、前端脚本、无需 API Key 的网页模式、云端真实公开网页抓取、三种文案生成、本地规则审查及模型未执行标识、Markdown / JSON 导出、独立访客台账隔离均通过。离线检查使用明确的网页测试替身；公网验证使用实际联网请求。验证通过仅证明这些流程可运行，不代表产品档案中的认证或批次证据已核实。
