# alist
在玩客云上部署小雅alist

## 一、获取token等三个链接

1、mytoken.txt的token获取链接（二选一）

https://aliyuntoken.vercel.app/

https://alist.nn.ci/zh/guide/drivers/aliyundrive.html

2、myopentoken.txt的token获取链接

https://alist.nn.ci/zh/guide/drivers/aliyundrive_open.html

3、temp_transfer_folder_id.txt的阿里云盘转存目录ID获取链接

（实测在阿里云资源盘的资源库里新建一个文件夹复制浏览器的URL就行了，以下是小雅官方提供的方法）

先转存这个

到自己网盘（选择资源盘），然后浏览器打开转存后的目录，浏览器的url

https://www.aliyundrive.com/s/rP9gP3h9asE 

最后一串就是，记得这个目录不要删，里面的内容可以定期删除

https://www.aliyundrive.com/drive/file/resource/640xxxxxxxxxxxxxxxxxxxca8a 



## 新版小雅alist安装脚本

用SSH工具登陆到你要安装小雅alist的主机终端，复制下面两行代码之中的一个，到命令行粘贴执行即可，必须提前部署好docker运行环境。

安装脚本1——bridge网络模式
```
bash -c "$(curl http://docker.xiaoya.pro/update_new.sh)"
```

安装脚本2——host网络模式
```
bash -c "$(curl http://docker.xiaoya.pro/update_new.sh)" -s host
```



二、用alist上挂载小雅alist

在ssh里安装alist的容器

打开ssh，用root账号登录。

输入代码安装alist：
```
docker run -d --restart=unless-stopped -v /etc/alist:/opt/alist/data -p 5244:5244 -e PUID=0 -e PGID=0 -e UMASK=022 --name="alist" xhofe/alist:latest
```

高于v3.25.0版本

3.25.0以上版本将密码改成加密方式存储的hash值，无法直接反算出密码，如果忘记了密码只能通过重新 随机生成 或者手动设置# 随机生成一个密码

```
docker exec -it alist ./alist admin random
```

# 手动设置一个密码,`NEW_PASSWORD`是指你需要设置的密码
```
docker exec -it alist ./alist admin set NEW_PASSWORD
```

查看小雅alist的token
```
docker exec -i xiaoya sqlite3 data/data.db <<EOF
select value from x_setting_items where key = "token";
EOF
```



三、小雅alist配置tvbox的地址

在TVBOX 的配置地址填入 http://192.168.2.1:5678/tvbox/my.json
```
http://192.168.8.8:5678/tvbox/my.json
```
 
注：将IP换成你自己的，且须提前在小雅配置目录中配置好docker_address.txt文件，里面内容就是你的小雅alist的访问IP加5678，不要带斜杠。

四、获取小雅元数据

如果单独下载和解压来获取（config.mp4和All.mp4即可）：

将以下命令后的两处挂载路径替换为你的实际路径，然后复制所有内容，粘贴到玩客云的ssh中执行，如果你按视频步骤和路径安装的，则不需要替换。
```
bash <(curl -sSLf https://xy.ggbond.org/xy/wky_xy_ailg.sh) /小雅emby的config路径 /小雅emby的xiaoya路径

示例（默认）：

bash <(curl -sSLf https://xy.ggbond.org/xy/wky_xy_ailg.sh) /mnt/sda1/xiaoya/config /mnt/sda1/xiaoya/xiaoya

bash <(curl -sSLf https://xy.ggbond.org/xy/wky_xy_ailg.sh) /media/devmon/sda1-usb-Generic_STORAGE_/xiaoya/config /media/devmon/sda1-usb-Generic_STORAGE_/xiaoya/xiaoya
```


五、定时清理阿里云盘转存目录的脚本
```
bash -c "$(curl -s https://xiaoyahelper.zngle.cf/aliyun_clear.sh | tail -n +2)" -s 3 -tg
```

注：上面这个命令后缀-tg可以删除，这个是电报机器人接收通知。

六、小雅emby的整合脚本（适用于arm64位和X86架构，不适用与玩客云）
```
bash -c "$(curl -sLk https://ddsrem.com/xiaoya_install.sh)"

#备用地址

bash -c "$(curl -sLk https://cdn.jsdelivr.net/gh/DDS-Derek/xiaoya-alist@latest/main.sh)"
bash -c "$(curl -sLk https://raw.githubusercontent.com/DDS-Derek/xiaoya-alist/master/main.sh)"
```
