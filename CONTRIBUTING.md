# 如何提交角色包条目（roles.json）

## 0. 你提交的是“索引条目”，不是角色包文件

`roles.json` 是一个只读索引，角色包文件本体可以放在：

- GitHub Release
- 对象存储（R2/OSS/COS…）
- 网盘（可选）

你只需要在索引里提供镜像链接与 SHA-256，客户端安装时会校验哈希，避免“链接被替换”。

## 1. 先准备角色包文件（.ocpak / .zip）

建议使用 `.ocpak`（本质是 zip 容器），内容结构应与运行时 `roles/{roleId}/` 一致，并包含 `manifest.json`。

## 2. 计算 SHA-256（必须）

Windows PowerShell：

```powershell
Get-FileHash -Algorithm SHA256 .\your_role.ocpak | Select-Object -ExpandProperty Hash
```

输出的 64 位十六进制字符串就是 `downloads[].sha256`。

> 同一版本的所有镜像（GitHub/网盘/对象存储）应指向**同一个文件 bytes**，因此 sha256 应一致。

## 3. 编辑 roles.json

### 3.1 顶层结构

```json
{
  "generatedAt": "2026-04-28T00:00:00Z",
  "roles": []
}
```

### 3.2 角色条目模板

```json
{
  "type": "role",
  "id": "your_role_id",
  "name": "你的角色名",
  "description": "一句话简介（不要太长）",
  "author": "作者名",
  "version": "1.0.0",
  "minRuntimeVersion": "0.2.0",
  "tags": ["治愈", "日常"],
  "downloads": [
    {
      "label": "GitHub Release",
      "kind": "direct",
      "url": "https://example.com/your_role.ocpak",
      "sha256": "64_hex_chars...",
      "trust": "community"
    }
  ]
}
```

### 3.3 downloads.kind 取值

- `direct`：直链下载（客户端可一键安装）
- `page`：下载页（客户端会打开链接，提示手动下载后“导入压缩包”）
- `pan`：网盘类（同上，风险提示更强）

### 3.4 trust（可选）

用于客户端排序与风险提示：

- `official | verified | community | unknown`

## 4. 提交 PR

请在 PR 描述里附上：

- 角色包简介
- 下载镜像列表
- SHA-256（与 `roles.json` 一致）
- 适配的最低运行时版本（如有）

## 5. 基本审核规则

为了保护用户：

- 必须提供 `downloads[].sha256`
- 链接失效需提交者负责更新
- 不要在 `note` 里写个人隐私信息

