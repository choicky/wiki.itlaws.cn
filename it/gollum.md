## 安装 gollum
    sudo apt-get install ruby ruby-dev make zlib1g-dev libicu-dev build-essential git
    sudo gem install gollum

## 安装支持中文的 rugged adapter
根据[基于gollum的wiki搭建](https://my.oschina.net/u/140938/blog/710743)，gollum 目前使用的adapter是grit，这货不支持中文目录以及中文文件名。Rugged这个adapter才支持中文目录以及中文名称。

    sudo gem install gollum-rugged_adapter

## 开启 Github 的 repository 的 wiki
参考[GitHub + Gollum 搭建个人Wiki](http://koyo922.github.io/2016/02/05/gollum/)

## 手工启动gollum
将repository的wiki下载到本地

    cd ~    
    git clone git@github.com:{username}/{repository}.wiki.git wiki.itlaws.cn

切换到wiki文件夹并运行

    cd ~/wiki.itlaws.cn
    gollum --adapter rugge

终端输出类似于下面的信息就表示运行成功。

    [2016-12-01 07:27:25] INFO  WEBrick::HTTPServer#start: pid=17197 port=4567

默认情况下，gollum服务占用4567端口。可通过`--port xxxx`参数来改变。

## 使gollum长期运行
安装supervisor：

    sudo apt install supervisor
    sudo service supervisor start

增加gollum的配置文件

    sudo nano /etc/supervisor/conf.d/gollum.conf

内容如下：

    [program:gollum]
    command = gollum --adapter rugged
    directory = /home/choicky/wiki.itlaws.cn
    user = choicky
    autostart = true
    autorestart = true
    stdout_logfile = /var/log/supervisor/gollum.log
    stderr_logfile = /var/log/supervisor/gollum_err.log

然后，运行

    sudo server supervisor reload
 
## 配置 Nginx
默认情况下，gollum服务占用4567端口。可通过`--port xxxx`参数来改变。

    server {
        listen 80;
        server_name wiki.itlaws.cn;

        location / {
            proxy_set_header   X-Real-IP $remote_addr;
            proxy_set_header   Host      $http_host;
           proxy_pass         http://127.0.0.1:4567;
        }
    }

