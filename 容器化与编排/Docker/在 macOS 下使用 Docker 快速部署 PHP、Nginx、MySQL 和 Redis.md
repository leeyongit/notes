在 macOS 下使用 Docker 快速部署 PHP、Nginx、MySQL 和 Redis 的步骤如下：

### 1. 安装 Docker
首先，确保你已经安装了 Docker。如果还没有安装，可以从 [Docker 官网](https://www.docker.com/products/docker-desktop) 下载并安装 Docker Desktop for Mac。

### 2. 创建项目目录
创建一个项目目录，用于存放 Docker 配置文件和应用程序代码。

```bash
mkdir my-php-app
cd my-php-app
```

### 3. 创建 `docker-compose.yml` 文件
在项目目录下创建一个 `docker-compose.yml` 文件，用于定义和运行多个 Docker 容器。

```yaml
version: '3.8'

services:
  nginx:
    image: nginx:latest
    container_name: nginx
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf
      - ./src:/var/www/html
    depends_on:
      - php
      - mysql
      - redis

  php:
    image: php:7.4-fpm
    container_name: php
    volumes:
      - ./src:/var/www/html
    depends_on:
      - mysql
      - redis

  mysql:
    image: mysql:5.7
    container_name: mysql
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: mydb
      MYSQL_USER: user
      MYSQL_PASSWORD: password
    ports:
      - "3306:3306"
    volumes:
      - mysql-data:/var/lib/mysql

  redis:
    image: redis:latest
    container_name: redis
    ports:
      - "6379:6379"
    volumes:
      - redis-data:/data

volumes:
  mysql-data:
  redis-data:
```

### 4. 创建 Nginx 配置文件
在项目目录下创建一个 `nginx.conf` 文件，用于配置 Nginx。

```nginx
server {
    listen 80;
    server_name localhost;

    root /var/www/html;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_pass php:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }
}
```

### 5. 创建 PHP 应用程序
在项目目录下创建一个 `src` 目录，并在其中创建一个简单的 PHP 文件 `index.php`。

```bash
mkdir src
echo "<?php phpinfo(); ?>" > src/index.php
```

### 6. 启动 Docker 容器
在项目目录下运行以下命令启动 Docker 容器：

```bash
docker-compose up -d
```

### 7. 访问应用程序
打开浏览器，访问 `http://localhost`，你应该能看到 PHP 的信息页面。

### 8. 停止和清理
如果你想停止并删除容器，可以运行以下命令：

```bash
docker-compose down
```

### 9. 其他操作
- **进入容器**：你可以使用 `docker exec -it <container_name> /bin/bash` 进入容器内部进行操作。
- **查看日志**：使用 `docker-compose logs <service_name>` 查看特定服务的日志。

### 总结
通过以上步骤，你可以在 macOS 上使用 Docker 快速部署 PHP、Nginx、MySQL 和 Redis 的开发环境。你可以根据需要进一步定制和扩展这个配置。