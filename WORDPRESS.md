# WordPress 集成说明

本帮助为**纯静态站点**（HTML + JS + PNG），无需 PHP 或数据库。适合上传到 WordPress 的 `wp-content/uploads/` 目录，或在任意静态主机/CDN 上托管。

## 推荐目录结构

将整个导出文件夹（例如 `尼康 F90X-F90 操作指南/`）上传到：

```
/wp-content/uploads/nikon-f90-guide/
```

上传后应能直接访问：

```
https://你的域名/wp-content/uploads/nikon-f90-guide/index.html
```

## 三种展示方式

### 1. 独立页面（最简单）

在 WordPress 菜单或文章中添加链接，指向：

```
https://你的域名/wp-content/uploads/nikon-f90-guide/index.html
```

用户在新标签页打开完整帮助，含目录侧栏，适合「操作手册」入口。

跳转到具体主题（可分享深链接）：

```
https://你的域名/wp-content/uploads/nikon-f90-guide/index.html#topic/f90j.t00000c
```

### 2. iframe 嵌入文章/页面（推荐）

1. 打开导出包中的 `wordpress-snippet.html`
2. 将 `base_url` 改为你的实际上传路径
3. 在 WordPress **自定义 HTML** 区块中粘贴代码

嵌入模式使用 `index.html?embed=1`（或 `embed.html`）：

- 默认先显示正文，目录通过左上角「目录」按钮打开（抽屉式）
- 窄屏与 iframe 内自动适配
- 通过 `postMessage` 向父页面报告高度，iframe 可随内容增高

**链接到特定主题（嵌入页）：**

```
https://你的域名/wp-content/uploads/nikon-f90-guide/index.html?embed=1#topic/f90j.t015578
```

### 3. 全宽页面模板

若主题支持「全宽 / 无侧边栏」模板：

1. 新建 WordPress 页面，选全宽模板
2. 仅放一个自定义 HTML 区块，使用上面的 iframe 代码
3. 将 iframe `min-height` 设为 `85vh` 或依赖自动高度脚本

## 安全与缓存

- 所有资源为静态文件，无服务端执行
- 建议在主机面板或 CDN 中对 `help-data.js` 与 `assets/` 开启长期缓存；更新手册后需刷新缓存或改文件名
- iframe 的 `postMessage` 仅用于高度同步，不传递用户数据

## 常见问题

**图片不显示**

- 确认 `assets/f90j/` 与 `index.html` 在同一目录树下
- 确认上传完整，未遗漏 PNG 文件

**iframe 高度不对**

- 使用包内 `wordpress-snippet.html` 中的高度脚本
- 部分主题会限制 iframe CSS，检查是否有 `max-height` 覆盖

**与主题样式冲突**

- 帮助 CSS 全部内联在 `index.html` 内，一般不受主题影响
- 仅 iframe 外层 `div` 可能受主题 margin 影响，可加 `margin:0;padding:0`

## 更新流程

在开发机重新运行：

```powershell
python scripts/export_standalone_viewer.py --locale zh-Hans --bundle f90j
# 网站分发推荐 WebP：加 --webp（默认无损）；更小体积：--webp --webp-lossy
```

用 FTP/主机文件管理器覆盖 `wp-content/uploads/nikon-f90-guide/` 下文件即可（保留相同 URL）。

WebP 在 iOS 14+、Chrome、Firefox、Edge 及近年 Safari 中均支持，适合 WordPress 托管的静态资源。
