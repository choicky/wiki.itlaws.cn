[hugo](https://gohugo.io/) 是一个用于将 MarkDown 文件转换为 Html 网页的程序，也就是所谓的静态网站。

# 安装
到 [https://github.com/spf13/hugo/releases](https://github.com/spf13/hugo/releases) 下载对应平台的二进制包，丢到系统路径里面，例如 `/usr/local/bin/` 下面，改命为 `hugo`。

# 使用

## 建立网站文件夹
    hugo new site ~/itlaws.cn

## 下载 theme
到 [http://themes.gohugo.io/](http://themes.gohugo.io/ ) 下载一个模板，丢到网站文件夹的 `theme` 下面。
或者用 `git` 命令下载一个，例如下载一个名为 `hugo_theme_robust` 的 theme:
```
$ cd ~/itlaws.cn/themes
$ git clone https://github.com/dim0627/hugo_theme_robust.git
```

## 新建测试页面

在网站文件夹目录下运行：

```
hugo new about.md
hugo undraft content/about.md
```

上述命令会在 `content` 文件夹新建 `about.md` 并去掉其草稿属性。
打开 about.md 并编辑其中的内容。

## 测试

```
$ cd ~/itlaws.cn/
$ hugo --theme=hugo_theme_robust
```

正常情况下，会在 `public` 文件夹生成了 `html` 静态文件。

## Nginx 配置


```
server {
        listen 80;

        root /home/choicky/itlaws.cn/public;

        index index.html index.htm index.nginx-debian.html;

        server_name itlaws.cn;

        location / {
                try_files $uri $uri/ =404;
        }
}
```
