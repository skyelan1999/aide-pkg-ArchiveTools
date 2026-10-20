# Archive Tools · Aide 压缩文件

Aide `archive-tools` 插件的独立分发仓库，版本 **1.0.0**，MIT 许可。运行代码及安装 ZIP 与[原市场来源](https://github.com/skyelan1999/aide/tree/4dc9a7b0b01fef6ac3550b0b0fcf97db42d45f94/plugins/archive-tools)保持一致。

## 能力

两个工具均通过 Aide 原生归档执行器处理，属于写入操作，沿用宿主工具权限和审批流程。

`archive_compress` 将当前工作区中的文件或文件夹压缩到原路径旁，支持 zip、tar、tar.gz、tar.bz2、tar.xz：

```json
{"path":"reports","format":"zip"}
```

该示例创建 `reports.zip`，不会覆盖已存在的压缩目标；目标存在时报错。

`archive_extract` 解压当前工作区中的归档，在同级创建新目录：

```json
{"path":"reports.zip"}
```

支持 ZIP、TAR、TAR.GZ/TGZ、TAR.BZ2/TBZ2、TAR.XZ/TXZ，以及独立 GZ/BZ2/XZ 压缩流。目标目录重名时自动使用 `reports (2)` 等新名称，不覆盖原目录。拒绝路径穿越、绝对路径、规范化后的重复路径、链接和特殊文件。不支持 RAR、7z 或加密 ZIP。

## 依赖与范围

**需要 Aide 原生 `archive_compress` / `archive_extract` 执行器**及文件归档功能。Node 入口只注册工具协议；直接调用其 handler 会明确报错。安装 ZIP 不能为缺少执行器的旧宿主补充功能。

ZIP 不包含宿主执行器或 Python。TAR.BZ2 创建以及 XZ 编解码依赖 Aide 运行环境中可调用的 `python3`，并需标准库 `bz2` / `lzma`；无需额外 npm/Python 第三方包。ZIP、TAR、GZip 及 BZip2 读取使用 Go 归档/压缩支持。

工具参数使用**相对当前工作区根目录**的路径，没有 `source` 参数。宿主文件界面另有引用来源操作，但不能将其等同于插件工具支持任意来源路径。SSH 工作区沿用 Aide 的 SFTP 读写与已配置身份，归档处理在 Aide 服务端完成；插件不接收密码或私钥。本次真实 SSH 端到端未实测。

归档功能有条目数量、内容大小和编解码超时限制，不适合无限体积备份。保留原文件；权限和文件属性处理以宿主实现为准，不承诺完整系统备份或保留所有元数据。

## 安装与校验

市场版本基线为 `0.1.17.0 RC2`，同时需要上述原生执行器。平台声明 Windows、macOS、Linux；本次实际验证 Linux，Windows/macOS 未实测。

从 [v1.0.0 Release](https://github.com/skyelan1999/aide-pkg-ArchiveTools/releases/tag/v1.0.0) 的固定提交链接下载 ZIP 和 SHA256SUMS，校验后通过 Aide 插件管理导入，或在[插件市场](https://skyelan1999.github.io/aide-pkg-shop/)搜索 `archive-tools`。

```sh
sha256sum -c SHA256SUMS
```

安装包位于 `packages/v1.0.0/`，保留原 LICENSE、index.js、manifest.json，未重新打包；已安装相同 ID/版本无需重复安装。Release 提供固定提交仓库下载链接，没有另行上传二进制附件。宿主应用启动沿用 `start.command`。

## 验证

发布验证覆盖五种压缩格式的创建/解压往返、文件下载与目录打包、同名解压目录避让、Windows 风格路径规范化、路径穿越/重复路径及 TAR 链接拒绝。另验证 Aide 插件宿主注册及 handler 的原生执行器约束。精确命令与结果见 `docs/release-verification.json`。

本次仅独立分发与市场元数据更新，无宿主代码、镜像或产品 UI 变更。SHA-256 用于完整性校验，不是发布者签名；Node 插件在宿主执行，插件机制不是权限沙箱。
