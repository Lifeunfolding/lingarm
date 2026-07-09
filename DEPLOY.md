# LingArm 门户 · 弹性云部署指南

> 把多页站点部署到你自己的弹性云服务器，让所有链接可在线访问。

## 站点结构

```
web/
├── lingarm_portal.html      # 门户主页（入口）
├── pages.css                # 子页共享样式
└── pages/
    ├── train.html           # train/ baseline 详情
    ├── train_1.1.html       # train_1.1/ 双路线详情
    ├── project.html         # project/ 方案文档
    └── model.html           # FrozenVLA-2B 模型卡
```

所有内部链接均为相对路径，部署到任何静态服务器都能直接跳转。

---

## 部署到弹性云服务器（nginx）

### 1. 上传文件到服务器

把整个 `web/` 目录传到服务器。假设服务器 IP 为 `YOUR_SERVER_IP`，用户名 `root`：

```bash
# 在本地执行（Git Bash / PowerShell 均可）
scp -r "G:/LingArm blueprint/web" root@YOUR_SERVER_IP:/var/www/lingarm
```

或用 rsync（增量同步，推荐）：

```bash
rsync -avz --delete "G:/LingArm blueprint/web/" root@YOUR_SERVER_IP:/var/www/lingarm/
```

### 2. 安装 nginx

```bash
# Ubuntu / Debian
ssh root@YOUR_SERVER_IP
apt update && apt install -y nginx

# CentOS / RHEL / 麒麟
yum install -y nginx
```

### 3. 配置 nginx 站点

新建 `/etc/nginx/conf.d/lingarm.conf`：

```nginx
server {
    listen 80;
    server_name your-domain.com;   # 没域名就填服务器 IP 或 _
    root /var/www/lingarm;
    index lingarm_portal.html;

    # 门户主页
    location = / {
        try_files /lingarm_portal.html =404;
    }

    # 子页
    location / {
        try_files $uri $uri/ /lingarm_portal.html;
    }

    # 静态资源缓存
    location ~* \.(css|js|png|jpg|svg)$ {
        expires 7d;
    }

    # 安全头
    add_header X-Content-Type-Options nosniff;
}
```

### 4. 启动

```bash
nginx -t              # 测试配置
systemctl enable nginx
systemctl restart nginx
```

浏览器访问 `http://YOUR_SERVER_IP` 即可看到门户，所有子页链接均可跳转。

### 5. （可选）配置 HTTPS

```bash
apt install -y certbot python3-certbot-nginx
certbot --nginx -d your-domain.com
```

---

## 常见问题

| 问题 | 解决 |
|------|------|
| 访问空白 | 检查 `root` 路径是否正确，`lingarm_portal.html` 是否在该目录 |
| 子页 404 | 确认 `pages/` 目录已上传，nginx `try_files` 配置正确 |
| 样式丢失 | 确认 `pages.css` 在 web 根目录，子页用 `../pages.css` 引用 |
| 中文乱码 | nginx 默认不指定 charset，HTML 已声明 UTF-8，一般无问题；如有可在 nginx 加 `charset utf-8;` |
| 权限 | `chown -R www-data:www-data /var/www/lingarm`（Ubuntu）或 `nginx:nginx`（CentOS） |

---

## 域名与备案

- **有域名**：把 `server_name` 改为你的域名，DNS A 记录指向服务器 IP
- **国内服务器**：域名需 ICP 备案才能用 80 端口；未备案可先用 IP 访问，或用非常规端口（如 8080）
- **无域名**：直接用 `http://YOUR_SERVER_IP` 访问即可

---

## 更新站点

以后修改了本地文件，重新同步即可：

```bash
rsync -avz --delete "G:/LingArm blueprint/web/" root@YOUR_SERVER_IP:/var/www/lingarm/
```

无需重启 nginx（静态文件实时生效）。
