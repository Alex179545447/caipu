# 阿里云服务器部署指南

## 目标域名
- **域名**: caipu.zhimai-ai.cn
- **目标目录**: /www/caipu.zhimai-ai.cn

---

## 第一步：域名解析配置

1. 登录阿里云 DNS 控制台
2. 添加 DNS 记录：

| 记录类型 | 主机记录 | 记录值 | TTL |
|---------|---------|-------|-----|
| A | caipu | 你的服务器IP | 600 |

3. 等待5-10分钟生效

---

## 第二步：服务器环境准备

SSH连接到你的阿里云服务器：

```bash
ssh root@你的服务器IP
```

### 安装 Nginx

```bash
# CentOS/RHEL
sudo yum install nginx -y

# Ubuntu/Debian
sudo apt update && sudo apt install nginx -y
```

### 启动 Nginx

```bash
sudo systemctl start nginx
sudo systemctl enable nginx
```

### 配置防火墙

```bash
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --reload
```

---

## 第三步：创建网站目录

```bash
# 创建网站目录
sudo mkdir -p /www/caipu.zhimai-ai.cn

# 设置权限
sudo chown -R $USER:$USER /www/caipu.zhimai-ai.cn
```

---

## 第四步：上传代码

### 方式1：SCP上传

从本地上传：
```bash
scp -r D:/workspace/prototype/* root@你的服务器IP:/www/caipu.zhimai-ai.cn/
```

### 方式2：Git拉取

在服务器上安装Git并克隆仓库：
```bash
# 安装Git
sudo yum install git -y  # CentOS
# 或
sudo apt install git -y  # Ubuntu

# 克隆仓库（创建好GitHub仓库后）
cd /www
sudo git clone https://github.com/你的用户名/meal-planner.git caipu.zhimai-ai.cn
```

---

## 第五步：配置 Nginx

创建Nginx配置文件：

```bash
sudo nano /etc/nginx/conf.d/caipu.zhimai-ai.cn.conf
```

复制以下配置：

```nginx
server {
    listen 80;
    server_name caipu.zhimai-ai.cn;

    root /www/caipu.zhimai-ai.cn;
    index index.html;

    # Gzip压缩
    gzip on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml;
    gzip_min_length 1000;

    # 静态文件缓存
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2)$ {
        expires 30d;
        add_header Cache-Control "public, immutable";
    }

    # SPA路由支持
    location / {
        try_files $uri $uri/ /index.html;
    }

    # 安全 headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;

    # 访问日志
    access_log /var/log/nginx/caipu_access.log;
    error_log /var/log/nginx/caipu_error.log;
}
```

测试并重载Nginx：

```bash
# 测试配置
sudo nginx -t

# 重载配置
sudo systemctl reload nginx
```

---

## 第六步：配置SSL证书（可选但推荐）

### 使用 Let's Encrypt 免费证书

```bash
# 安装 certbot
sudo yum install certbot python3-certbot-nginx -y  # CentOS
# 或
sudo apt install certbot python3-certbot-nginx -y  # Ubuntu

# 申请证书
sudo certbot --nginx -d caipu.zhimai-ai.cn

# 自动续期测试
sudo certbot renew --dry-run
```

证书会自动续期，无需手动处理。

---

## 第七步：验证部署

访问 https://caipu.zhimai-ai.cn 检查是否正常显示。

---

## 日常更新流程

### 1. 更新代码到 GitHub

```bash
# 在本地项目目录
git add .
git commit -m "更新内容描述"
git push origin main
```

### 2. 在服务器拉取更新

```bash
cd /www/caipu.zhimai-ai.cn
git pull origin main
```

### 或者配置自动部署钩子

创建 GitHub Webhook：
1. 进入 GitHub 仓库 → Settings → Webhooks
2. 添加 webhook：
   - Payload URL: `https://caipu.zhimai-ai.cn/webhook`
   - Content type: `application/json`
   - Secret: 设置一个密钥

创建服务器接收脚本 `/www/caipu.zhimai-ai.cn/webhook.php`：

```php
<?php
$secret = '你的webhook密钥';
$payload = file_get_contents('php://input');
$signature = hash_hmac('sha256', $payload, $secret);

if (hash_equals($signature, $_SERVER['HTTP_X_HUB_SIGNATURE_256'])) {
    exec('cd /www/caipu.zhimai-ai.cn && git pull origin main 2>&1');
    echo 'Updated';
}
```

---

## 故障排查

### 查看 Nginx 错误日志
```bash
sudo tail -f /var/log/nginx/caipu_error.log
```

### 检查 Nginx 状态
```bash
sudo systemctl status nginx
```

### 重启 Nginx
```bash
sudo systemctl restart nginx
```

---

## 目录权限说明

```
/www/caipu.zhimai-ai.cn/
├── index.html          # 主入口
├── pages/             # 页面文件
├── README.md          # 项目说明
└── .github/           # GitHub配置（可选）
```

---

**遇到问题？** 检查 `/var/log/nginx/error.log` 或重启 Nginx 服务。
