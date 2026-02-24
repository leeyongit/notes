
 ## **PHP 慢日志每天切割但不压缩（保留原始 `.log` 文件）**，
 只需要在 `logrotate` 配置中去掉压缩相关的配置项即可。

------

#### ✅ 修改 `/etc/logrotate.d/php-fpm-slow`

打开：

```bash
nano /etc/logrotate.d/php-fpm-slow
```

修改为如下内容 👇：

```bash
/www/server/php/74/var/log/slow.log {
    daily
    rotate 7
    missingok
    notifempty
    create 644 www www
    # 不压缩
    nocompress
    postrotate
        /etc/init.d/php-fpm-74 reload > /dev/null 2>/dev/null || true
    endscript
}
```

------

##### ⚙️ 参数解释：

| 参数                       | 含义                          |
| -------------------------- | ----------------------------- |
| `daily`                    | 每天轮转一次                  |
| `rotate 7`                 | 保留 7 天日志（超过自动删除） |
| `missingok`                | 日志不存在不报错              |
| `notifempty`               | 如果日志为空则不轮转          |
| `create 644 www www`       | 新日志文件的权限和属主        |
| `nocompress`               | ❗不压缩日志                   |
| `postrotate ... endscript` | 每次切割后重载 PHP-FPM        |

------

##### 🧪 手动测试

执行命令：

```bash
logrotate -f /etc/logrotate.d/php-fpm-slow
```

然后查看：

```bash
ls -lh /www/server/php/74/var/log/slow*
```

输出示例：

```
-rw-r--r-- 1 www www  5.3M Oct 11 00:00 slow.log
-rw-r--r-- 1 www www  7.8M Oct 10 00:00 slow.log-20251010
-rw-r--r-- 1 www www  6.9M Oct 09 00:00 slow.log-20251009
```

✅ 可以看到保留了 7 天的 `.log` 文件，没有 `.gz` 压缩文件。

------

##### 💡 补充建议

如果你不用压缩，建议：

- **定期清理**旧日志（通过 `rotate 7` 已自动控制）

- 日志量大的话，也可以用：

  ```bash
  rotate 3
  ```

  保留 3 天即可。

