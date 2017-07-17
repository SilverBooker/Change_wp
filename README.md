# Change_wp
一个适用于ubuntu的从网络获取图片并为其更换桌面shell脚本
1.设计思路：


（1）使用curl访问url。


（2）解析内容，用grep和sed解析输出url。


（3）用wget工具爬取图片。


（4）使用gsettings设置背景图。

2.程序内容：

#!/bin/bash


#定义图片目录路径
DIR_PIC=/usr/share/backgrounds


function debug

{

#输出参数
    echo $@
    
}

function downpaper
{

#解析desk.zol.com.cn/1920x1080/得到报文

local img=`curl -s http://desk.zol.com.cn/1920x1080/ \

#正则匹配http：开头.jpg结尾的行并标准化输出出来

              |grep -oP "http:.*jpg" \
              
#从29张图片地址中随机取出一行

              |sed -n $[$RANDOM%29+1]p \
              
#将该行中的s208x130替换成s1920x1080

              |sed 's/s208x130/s1920x1080/' \
              
#将该行中的s120x90替换成s1920x1080

              |sed 's/s120x90/s1920x1080/'`
              
    debug start downloading [$img]
    
    # test的-d是用来检测目录是否存在，前面的!是取反（如果不存在的话）
    
    if [ ! -d $DIR_PIC ]; then
    
        #如果不存在这个目录就创建它
        
        mkdir -p $DIR_PIC
        
    fi
    
    wget $img -O $DIR_PIC/paper.jpg
    
#如果下载的文件存在，就将其替换成壁纸，否则输出提示信息，返回1状态码。

    if [ -f "$DIR_PIC/paper.jpg" ];then
    
    gsettings set org.gnome.desktop.background picture-uri file://$DIR_PIC/Gift.jpg
    
    gsettings set org.gnome.desktop.background picture-uri file://$DIR_PIC/paper.jpg
    
    else
    
    echo "换壁纸失败"
    
    exit 1
    
    fi
}

#main函数


Downpaper
