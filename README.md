# rtcbotsrv
rtcbot Server是一个AI驱动的、基于实时音视频交互的视频见证服务框架，特别为基于RTC的视频见证类业务设计，内部集成了完整的业务流程所需的各种组件，可以快速构建基于RTC（实时音视频）、由AI数字人驱动的视频见证核心流程，支持工程化部署并方便集成到企业整体的视频业务流程中

# 特点
+实现了视频见证类业务流程的完全可配置化，包括话术的流转、分支以及参数化话术配置，后台的业务流程处理不需要代码级的开发，企业只需要把精力集中在业务集成和客户端开发上。  
+内部集成了所需的风险识别模块，仅仅通过配置化的业务流程就可以实现。内置了完整的AI模块（语音识别、人脸出入框检测、人脸比对、语音合成等）。  
+支持完全的内网部署（除了需要访问 rtc 平台），不需要访问任何云端第三方服务，rtcbotsrv 完全采用c++编写，不依赖于Python环境。  
+支持和业务数据接口的集成，主要是提供业务级的会话管理接口，方便和业务系统的集成。  
+支持本地服务器端的录音录像。业务会话完成后很短时间内就可以提供录制的业务视频。相比较云端的依赖于RTC服务商的录像，速度快下载方便。  
+内置了基础的数字人形象模块，可以不依赖于任何第三方组件实现基础的数字人虚拟视频。同时也提供高级数字人模块（需要单独部署），实现较为复杂的可定制化的数字人服务。  
+服务端部署：目前在 centos 上编译运行。  
+支持基于H5的纯浏览器客户端访问（包括安卓手机浏览器、苹果手机浏览器、windows上的Edge、Chrome浏览器等），方便与现有的APP集成或嵌入在微信小程序中，也支持用户基于RTC平台提供的安卓、iOS、Windows Native API接口开发客户端。  
+目前基于声网（Agroa RTC）平台开发，未来计划也支持 阿里 mPaaS rtc 和 网易 NRTC。  

# 应用场景
+保险视频回访  
+远程视频开户  
+贷款视频面签  
+在线视频问答  
+理财产品视频签约  
...  

# 试用版下载

+rtcbot v1.0安装包文件：deploy-2023-11-29.tar.gz。下载链接：https://pan.baidu.com/s/1lxBqsf30rhQL9r5QYzURLg 提取码：a2d5

+安装包包含了编译好的rtcbot server可执行程序和依赖的所有库和模型文件和资源文件等，同时也包含一个简单的H5客户端样例，可以直接部署使用。

+安装包文件大约有1.4G左右，主要是包含的pytorch_cuda的共享库文件较大.

# 试用版安装说明

+资源要求：操作系统 centos 7以上，带Nvidia GPU，cuda>=10.2    

(1)最好新建一个普通用户 rtcbot, 对应的HOME目录是 /home/rtcbot , 来进行后面的操作。当然如果你是一个人玩的开发环境，用 root 用户进行后面的操作也没问题。 建议新建目录比如 /home/rtcbot作为安装目录。
（下面均假设安装目录是/home/rtcbot) ，并且执行下面操作的用户要对 /home/rtcbot 目录有读写权限和对/tmp目录有读写权限。  

(2)将下载的文件包：deploy-2023-11-29.tar.gz 放到 /home/rtcbot 目录下，解包:  
        <span>```cd /home/rtcbot    
		      tar xzvf deploy-2023-11-29.tar.gz  ```</span>

(3)进入 /home/rtcbot/server/bin 目录，编辑 setenv.sh ,将其中的  
       SERVER_ROOT="/home/work/rainbot/server" 修改为实际的server目录，在这里就是修改为 SERVER_ROOT="/home/rtcbot/server",    	   
      保存 setenv.sh后在 /home/rtcbot/server/bin 目录下执行 :  
    <span>```
	   chmod +x setenv.sh    
	   ./setenv.sh   ```</span>

(4) 在 /home/rtcbot/server/bin目录下启动服务:  
 <span>```
       cd /home/rtcbot/server/bin    
	      ./rtcbotsrv -c 1   ```<span> 
	   
	   说明：参数 -c 1 表示作为第1个通道的服务启动。同一台服务器上可以同时启动多个通道的server(只要服务器CPU，GPU，内存够用)。  
	   就用 -c 跟上相应的数字即可  
	   
	   重新启动也是执行同样操作(rtcbotsrv会自动杀死之前的相同通道的进程再重新启动)  
	   
(5) rtcbotsrv 默认是作为后台守护进程运行的。如果成功启动后，先等一会后查看进程是否运行中:  

      ps -ef|grep rtcbotsrv   
	  
	  也可以查看日志文件 /home/rtcbot/log/rtcbotsrv_1.log,如果最后一行显示Ready to receive command...表示服务正常启动  

    问题：最常见的启动问题就是找不到GPU或服务器cuda版本太低。rtcbotsrv 是需要安装cuda>=10.2的GPU环境。可以用 nvidia-smi 查看服务器上GPU是否正常以及相应的cuda版本  

作为服务器启动上面的步骤就完成了。  

下面说明如何启动安装包附带的一个(用Python编写)web客户测试客户端：  

(1) 进入/home/rtcbot/webdemo,编辑 webmain.py, 将27行的 avdata_home = "/home/work/rainbot/avdata" 修改为为实际的目录，在这里就是avdata_home = "/home/rtcbot/avdata"

   ( 注:如果服务正常启动，会自动创建 /home/rtcbot/avdata 目录)  

(2) 运行这个Python脚本需要安装 Flask,flask_cors 包。如果还没有安装的话，执行:  
         <span>```pip install Flask  
	pip install flask_cors   ```<span>
	
     	建议使用Python3.6以上版本  

(3) 注意到 webmain.py 的最后2行代码是：  
 <span>```
    app.run(host="0.0.0.0", port=8091, ssl_context=('server.crt', 'server.key'))    
    #app.run(host="0.0.0.0", port=8091)   ```<span>
	
	表明最好是用 https 的方式启动web服务。这是因为用手机浏览器打开测试页面时，需要用到摄像头和麦克风。目前主流的浏览器都不支持在非https的情况下使用摄像头和麦克风了。  
	
	所以你需要为Flask的web服务配置一个简单的https。这个网上很多写如何配置的文章，在这里就不再赘述。  
	
(4) 运行 python webmain.py   

(5) 用手机浏览器打开相应的地址(端口默认是8091)，比如 https://192.168.0.202:8091/static/index.html  
    可以看到4个演示场景的页面入口。点击进入后看到测试麦克分和摄像头的页面（默认使用前置摄像头）。如果测试麦克分和摄像头正常，点击关闭。  
	然后点击右上角的“开始”按钮，即可以开始和后台的数字人客户进行视频交互。  

# 关于声网账号  

+注意：目前的版本使用的声网的音视频（Agora rtc）平台，需要配置声网的账号  
+目前测试包中配置是我的个人的测试账号( 欢迎白嫖:-) ), 但不保证账户上随时有音视频流量额度。  
+如果需要可以登录声网(https://www.shengwang.cn/)创建自己的应用账号,然后修改配置文件 /home/rtcbot/server/rtc/globalcfg.ini中app_id和app_certificate  
（修改完成后需要重启 rtcbotsrv )  


# 其它
 一些说明参见： https://weibo.com/rtcbot  

 定制化开发可发邮件：rtcbot@163.com

