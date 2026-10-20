# 当前服务器架构

更新时间：2026-10-20（Asia/Shanghai）。当前生产图片服务器为 `beijing`，公网 IP `82.156.245.197`，Ubuntu24.04 / Nginx1.24.0。SSH私钥仅由本机SSH配置管理，不进入仓库。

## 请求链路

```text
手机小程序（页面、馆藏文字和随包图片位于小程序代码包）
  └─ HTTPS 82.156.245.197:443 → 北京 Nginx
       └─ /miniprogram-assets/museum/...
            └─ /var/www/naiwa-miniprogram/current/museum/...

Let’s Encrypt HTTP-01 验证 → 北京 :80 /.well-known/acme-challenge/...
  └─ /var/lib/naiwa-acme
```

所有298张馆藏图片均存放在北京磁盘，部署直接读取本仓库资源。不存在向上海的反向代理、备用地址、图片回源或证书续期依赖。没有小程序业务数据库、云函数或Node常驻后端。站点根路径返回404，公开图片路由仅支持GET/HEAD，目录索引关闭，缓存一天。

## 实际目录与依赖

| 用途 | 北京服务器路径/配置 |
| --- | --- |
| 当前图片版本 | /var/www/naiwa-miniprogram/releases/assets-20261020145403707 |
| 生效入口 | /var/www/naiwa-miniprogram/current → 上述版本（未来以实际链接为准） |
| 文件数量 | museum/artworks/149张原图，museum/thumbnails/149张缩略图，约249.85 MiB |
| HTTP站点 | /etc/nginx/sites-available/naiwa-http（sites-enabled中软链接） |
| HTTPS站点 | /etc/nginx/sites-available/naiwa-https（sites-enabled中软链接） |
| 图片路由 | /etc/nginx/snippets/naiwa-miniprogram-assets.conf |
| ACME路由 | /etc/nginx/snippets/naiwa-acme.conf |
| 证书与私钥 | /etc/letsencrypt/live/naiwa-ip/，私钥仅留北京服务器 |
| 证书工具 | Python3.12独立环境 /opt/naiwa-certbot，Certbot5.4.0 |
| 续期钩子 | /usr/local/sbin/naiwa-certbot-deploy-hook |
| 续期服务/定时器 | /etc/systemd/system/naiwa-certbot-renew.service / .timer |

部署前逐文件验证SHA-256，通过后原子切换current；旧版本留在北京供回滚。小程序根目录无第三方npm依赖。服务端系统依赖为Nginx、rsync、Python3.12/venv和OpenSSH；证书工具依赖快照及站点源码位于deploy/。

## 证书与自动续期

为北京IP新签发Let’s Encrypt免费IP证书，SAN为IP Address:82.156.245.197，当前颁发链为YE1，到期时间为北京时间2026-10-27 13:57:05。后续以实际证书日期为准。

IP证书约160小时有效。定时器按Asia/Shanghai每天00:00、08:00、16:00检查，随机延迟0–30分钟，支持停机补执行。Certbot内部随机等待已关闭，避免重复延迟。更新证书后执行nginx -t，通过后reload。已完成含部署钩子的续期dry-run。80验证入口和443图片入口均需云平台放行。当前无通知邮箱，接手者需自行安排监测；无额外云存储套餐，流量沿用现有主机计费。

```sh
ssh beijing 'sudo nginx -t'
ssh beijing 'systemctl list-timers naiwa-certbot-renew.timer --no-pager'
ssh beijing 'sudo journalctl -u naiwa-certbot-renew.service -n 30 --no-pager'
ssh beijing 'sudo /opt/naiwa-certbot/bin/certbot certificates'
```

## 网络验证与微信边界

北京公网HTTP与HTTPS均已验证。2026-10-20 23:03（Asia/Shanghai）用户放行北京入站TCP443后，从开发电脑直连公网逐一验证298个图片URL，状态200、严格TLS证书校验、MIME类型和文件大小全部通过；两张实际下载图片的SHA-256与本地一致。详见[验证记录](VALIDATION.md)。手机验收需重新上传后的版本，公网验证不替代手机验收。

IP HTTPS不能填入微信request/downloadFile合法域名。当前仅用image和wx.previewImage加载图片；手机实际行为仍需用新上传版本验收。未来增加需域名能力的接口时，应另接合规域名。

## 旧服务器说明

上海旧服务器SSH别名现为shanghai，维护者在2026-10-20告知其约24天后到期。上海原网站没有在本次图片迁移中搬到北京；小程序当前功能不依赖该网站。已经上传的旧小程序代码仍含旧IP，需要重新编译、上传并设置体验版/发布新版。北京服务器部署无法远程改写旧代码包；使用新地址的版本可独立于上海运行。

北京当前监听：公网22（SSH）、80/443（Nginx）；系统DNS仅在本机回环地址。内存约3.6 GiB、根盘40 GiB。
