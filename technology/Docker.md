### Docker

* #### Docker 镜像：一个Docker项目的整体，相当于一个root文件系统.

  Docker有一个镜像仓库，用户可以直接从仓库中pull镜像；当用户只用某个镜像时，若本体主机中没有该镜像，则会自动从镜像仓库中下载相应image

  关于镜像的一些命令和说明：

  ```shell
  #查看本机的所有的docker镜像
  $ docker images
  #在仓库中搜索docker镜像
  $ docker search unbuntu
  #从仓库中下载docker镜像
  $ docker pull hello-world
  #删除镜像
  $ docker rmi hello-world
  ```
  - 保存与载入镜像：

    ```shell
    $ docker save -o 文件名(路径) 镜像名
    $ docker load --input 文件名
    ```

  Image的属性和含义：

  |REPOSITORY|TAG|IMAGE ID|CREATED|SIZE|
  |-------|-------|-------|--------|----|
  | 仓库源| 镜像标签|镜像ID|镜像创建时间|镜像大小|

  

* #### Docker 容器：镜像的运行态，相当于是对一个镜像的实例化

  运行容器：

  ```shell
  # run from a image
  # -i:交互式操作
  # -t:终端
  # -d:后台运行
  # ubuntu:镜像
  # /bin/bash:命令
  $ docker run -i -t ubuntu /bin/bash
  # stop a container
  $ docker stop container_id
  # 查看所有容器
  $ docker ps -a
  $ docker start container_id # 启动已经停止的容器
  # 后台运行时，进入后台
  $ docker attach container_id
  # 删除容器
  $ docker rm -f container_id
  ```

* #### Dockerfile

  一个用来构建镜像的文本文件，文本内容包含了一条条构建镜像所需的指令和说明。

  - FROM：定制基于FROM的镜像
  - RUN：用于执行命令行命令，在build镜像时执行，命令每执行一次就会在docker上新建一层

  ```dockerfile
  # 基于Ubuntu镜像定制
  FROM Ubuntu
  RUN command line
  RUN ["可执行文件", "参数1", "参数2"]
  #以上创建两层镜像，将两条命令用&&连接则不会创建新的层
  ```
  
  - 其他Dockerfile命令：
    
    FROM
  
      格式为FROM或FROM <image>:<tag>
      第一条指令必须为FROM指令。如果在同一个Dockerfile中创建多个镜像时，可以使用多个FROM指令。
  
    MAINTAINER
  
      格式为MAINTAINER <name> ，指定维护者信息。
  
    RUN
  
      格式为RUN <command> 或RUN ["executable", "param1", "param2"]。
  
    CMD
  
      CMD ["executable","param1","param2"]使用exec执行，一个Dockfile只能有一条CMD，以最后一条为准。
  
    EXPOSE
  
      格式为EXPOSE <port>，容器暴露的端口号。
  
    ENV
  
      格式为ENV <key> <value> ， 指定一个环境变量。
  
    ADD
  
      格式为ADD <src> <dest>，src可以是URL或者tar文件。
  
    COPY
  
      格式为COPY <src> <dest>，src为本地目录的时候推荐使用
  
    VOLUME
  
      格式为VOLUME ["/data"]
  
	-  **从Dockerfile构建镜像：**
	
	  ```shell
	  $ docker build -t image_name:tag .
	  # -t 指定镜像的名称和标签
	  # . 为上下文路径
	  ```

 