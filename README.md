# OpenCloud · LazyCat

[OpenCloud](https://github.com/opencloud-eu/opencloud) 的懒猫微服 LPK 包装项目。

> 🌤️ OpenCloud is the open source platform for file management, sharing and collaboration. Simple and sovereign.

## 部署配置

设置向导只保留必要或常用的用户选项：

- 初始 `admin` 密码：必填，默认生成 24 位随机值，仅首次初始化时生效。
- 默认语言：ISO 639-1 代码，默认 `en`。
- Basic Auth：默认关闭；仅为不支持 OpenID Connect 的 WebDAV 客户端开启。

不配置账号自动填充，保留 OpenCloud 原生登录体验。本应用只发布到喵喵商店，因此按项目要求不集成懒猫文件选择器拦截。

## 运行与持久化

- 镜像：`docker.1ms.run/opencloudeu/opencloud-rolling:7.5.0`
- HTTP 端口：`9200`
- 配置：`/lzcapp/var/config` → `/etc/opencloud`
- 数据：`/lzcapp/var/data` → `/var/lib/opencloud`
- 运行身份：`1000:1000`
- 健康检查：镜像内置的 `opencloud proxy health`

Compose 中的 `csp.yaml`、`apps.yaml`、密码黑名单和 maps 扩展会放入 LPK `contentdir`，启动前复制到持久化配置/数据目录；不使用 LazyCat 不支持的单文件 bind。`setup_script` 执行 `opencloud init || true` 并修正目录归属，随后由镜像默认的 `/usr/bin/opencloud server` 入口启动服务。

## 镜像更新与发布

GitHub Action 跟踪 `opencloudeu/opencloud-rolling` 的三段式正式版标签，并将完整标签原样映射为应用版本。镜像通过 Docker Hub 内置加速器 `docker.1ms.run` 提供；`require_digest_match: true` 要求 Linux amd64 镜像与上游 digest 一致。该校验只读，不复制镜像到 LazyCat Registry。

工作流每日检查，也可手动运行。它会构建版本化 GitHub Release LPK，仅发布到喵喵商店；懒猫官方商店始终关闭。

所需 GitHub Actions Secrets：

- `APPSTORE_URL`
- `APPSTORE_TOKEN`
- `APP_ID`（首次发布成功后固定新应用的数字 ID）
