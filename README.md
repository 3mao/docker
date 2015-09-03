#Docker常用命令和FAQ翻译及注释

* QQ群：321380754
* 作者：luomg	

##Docker启动
	redhat7:/bin/systemctl  start docker.service
	设置自动启动
	redhat7:chkconfig docker on
##docker自动补全
##docker或取
	从默认注册服务器选取
		仓库名:tag名称
	指定注册服务器

##注册服务器
	默认注册服务器
	设定默认注册服务器
	命令行指定注册服务器
	查看注册服务器中的镜像

	


##docker命令行
docker选项		
		-a 			指定更新的用户的信息
		-m			指定提交注释
		-h,--help 打印帮助信息
		--add-registry=[]	
		--api-cors-header=""	设置远程调用域头。默认禁止。提供的url类似"http:/foo,fttp://bar
		-b,bridge=""		设定容器的的网络，桥接到一个已经存在的网络上面，默认为空，禁止桥接网络
		--bip=""		
		-t 			指定tag信息，e.g: -t="ubuntu/12.04:luomg"
	
命令
	build	从Dockerfile编译创建新的镜像，并返回ID,如果中途出错会返回，并暂存当前状态，再次启动从错误处开始,更多帮助：docker  build  --help
	export 	contaner_id > file.tar 导出容器到本地文件，类似快照,sudo	docker	export	7691a814370e	>	ubuntu.tar
	commit	基于某次提交改变镜像，并创建新的提交
	images 	列出拥有的镜像,e.g: docker images,显示列:仓库名		标签	ID号	提交时间	镜像大小
	import 	导入容器，e.g:	cat	ubuntu.tar	|	sudo	docker	import	-	test/ubuntu:v1.0
	load	--input file.tar 	载入文件到镜像
		<	file.tar	
	pull 	从注册服务器抓取一个镜像或者repository
	push	推送镜像到注册服务器
	rm	移除容器
	rmi	移除本地镜像,移除镜像前必须移除依赖该镜像的所有容器
	run	启动容器
		-d 	后台模式运行容器，并返回容器ID
		-t 	Docker会分配一个为终端给容器并绑定到标准输入,实现交互模式
		-v    host_dir:contaner_dir  挂载数据到容器，
		      e.g 	sudo	docker	run	-d	-P --name web	-v /src/webapp:/opt/webapp	training/webapp	python	app
		-i	让容器的标准输入保持打开
	save <image:tag> 导出镜像到本地， docker save -o ubuntu_12.04.tar.gz ubuntu:12.04
	search	"key"	从注册服务器搜索指定名称的镜像
	

端口映射

Dockerfile
使用Dockerfile编译镜像
docker	build	-t="ubuntu/12.04:luo"  ./
#cat  ./luo/Dockerfile
#this is comment
#指定基镜像	
FROM	ubuntu:12.04
MAINTAINER	Docker luomgf <luomgf@163.com>	#维护者信息
RUN	apt-get -qq update	#创建时执行安装
RUN	apt-get	-qqy install ruby	ruby-dev
RUN	gem	install sinatra	
#将物理主机中文件复制到docker /var/www
ADD	add_myfile	/var/www
#向外部开放80端口
EXPOSE 80
#默认容器启动后启动程序
CMD ["/usr/sbin/apachectl","-D","FOREGROUND"]


##镜像
	##镜像的构建
	1、从运行中的系统构建镜像，
		基于已有的镜像
		1.1、load
		load    --input file.tar        载入文件到镜像
                	<       file.tar
		1.2、impot 
		cat     ubuntu.tar      |       sudo    docker  import  -       test/ubuntu:v1.0
		基于linux发行办法构建类似lfs技术
		1.3、debootstrap[ubuntu]，febootstrap[fedora]
			debootstrap trusty trusty > /dev/null
			tar -C trusty -c . | sudo docker import - trusty
		1.4、Scratch
			tar cv --files-from /dev/null | docker import - scratch
		1.5、纯手工打造
			a、将更目录下的的文件除proc  boot 拷贝到新目录，chroot进入目录，打为tar包-->生成image
			b、用现有的基础镜像save xxx.tar ,解压chroot进入，安装相关软件，再打包，再导入
	2、基于镜像服务器构建镜像
		2.1、从dockerhub.io
		2.2、从代理服务器构建镜像，如：DaoCloud
	###镜像推送
	1、推送到中央服务器
		1.1、推送到docker hub
			http://m.blog.chinaunix.net/uid-20788636-id-5049772.html
		1.2、

	私有仓库的构建
		创建
		docker run -d -p 5000:5000 -v /root/my_registry:/tmp/registry registry
		默认通过registry镜像来实现，且镜像挂载在容器的/tmp/registry目录，这个地方通过-v实现数据共享
		push
		docker push 104.131.173.242:5000/ubuntu_sshd_gcc_gerry:14.04
		pull
		docker pull 104.131.173.242:5000/ubuntu_sshd_gcc_gerry:14.04
		search
		curl http://104.131.173.242:5000/v1/search
		https问题
			修改Docker配置文件
			vim /etc/default/docker
			增加以下一行
			DOCKER_OPTS="$DOCKER_OPTS --insecure-registry=104.131.173.242:5000"
			重启Docker
			sudo service docker restart
			refer: http://dockerone.com/question/21

##[参考资料]
	1、Docker —— 从入门到实践：http://dockerpool.com/static/books/docker_practice/index.html
	


* QQ群：321380754
* 作者：罗明刚	
