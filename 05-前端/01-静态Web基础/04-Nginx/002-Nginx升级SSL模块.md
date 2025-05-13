---
createTime: 2025-04-22
---
> ![note]
> 执行之前记得备份 nginx 目录，
> 完整流程后，会在 sbin 目录生成一个 nginx.old 文件，但避免意外，还是备份 nginx 目录

# 1. **检查并安装 Nginx 的 SSL 模块**

如果你使用的是一个没有 SSL 模块的 Nginx 版本，你需要重新编译 Nginx，并确保在编译时启用了 `--with-http_ssl_module` 选项。你可以按照以下步骤操作：

## 步骤 1: 检查 Nginx 是否已启用 SSL 模块

运行以下命令，检查当前 Nginx 是否启用了 SSL 模块：

bash复制代码

`nginx -V 2>&1 | grep --color=auto http_ssl`

如果输出中包含 `--with-http_ssl_module`，表示已启用 SSL 模块。如果没有，你需要重新编译 Nginx。

## 步骤 2: 重新编译并启用 SSL 模块

1. 下载 Nginx 源代码（如果没有的话），可以从 [Nginx 官方网站](http://nginx.org/en/download.html)下载。
    
2. 解压并进入 Nginx 源代码目录。
    
    bash复制代码
    
    `tar -zxvf nginx-<version>.tar.gz cd nginx-<version>`
    
3. 配置并启用 `--with-http_ssl_module`，然后编译 Nginx：
    
    bash复制代码
    
    `./configure --with-http_ssl_module make sudo make install`
    
4. 完成后，重新启动 Nginx：
    
    bash复制代码
    
    `sudo systemctl restart nginx`
    

## 步骤 3: 验证 SSL 模块是否启用

重新启动 Nginx 后，再次运行：

bash复制代码

`nginx -V 2>&1 | grep --color=auto http_ssl`

如果一切正常，你应该能够看到 `--with-http_ssl_module`，并且可以正常使用 SSL 配置。

