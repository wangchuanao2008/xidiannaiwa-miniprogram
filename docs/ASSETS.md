# 素材来源与存放约定

迁移日期：2026-10-20。来源为现有“奶蛙电子科技大学”静态网站的本地完整资源，不修改原网站。馆藏的图像和介绍来自已有奶蛙博物馆数据，作品稳定 ID 沿用 work-001 等原值。

- 校名校徽：原网站 naiwa/logo-edited.png 的完整高清 PNG。
- 轮播图：原网站 birthday.svg / welcome.svg 的最终合成效果，导出为 750×422 JPG。SVG 含内嵌原位图和修补图，随仓库保留，不依赖外部原图路径。
- 149 张原图及 149 张缩略图：复制到 assets/museum/；原图包含 28 张 JPG、121 张 WebP。清单与数据完整保留。
- 首页展馆封面：work-103 的缩略图，随小程序包提供。
- 开源卡片左图：网络检索的经典捧腹大笑奶蛙，来自[电玩帮文章](https://www.vgover.com/news/198094)，[原始图片地址](https://imgheybox.max-c.com/bbs/2026/01/15/b2651cd314d13862f5eb928ea8d2fc13/thumb.png)。图片实际格式为 JPEG，以 `miniprogram/assets/naiwa-laugh.jpg` 随包存放，未修改图片内容。
- 开源卡片右图：从 [GitHub 官方品牌素材包](https://brand.github.com/GitHub_Logos.zip)提取 `PNG/GitHub_Invertocat_Black.png`，保存为 `miniprogram/assets/github-mark.png`。用于标识项目 GitHub 仓库；[官方使用说明](https://brand.github.com/foundations/logo)。

这些资源均使用普通 Git 存储，不要求 Git LFS，也不从原网站目录读取。小程序包只包含少量首页图片，完整馆藏在仓库根 assets/，部署时另行上传。

本文件是技术溯源，**不是授权证明**。原作、衍生图、校徽和文字可能具有不同权利归属。维护者应在公开分发或商业使用前确认各项授权；当前未为全部资产或代码擅自选择许可证。后续若指定开源许可证，应说明其覆盖范围，图片例外单独列明。
