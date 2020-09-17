## 镜像操作

1. 检索：docker search **
2. 拉取: docker pull **
3. 查看本地镜像：docker images
4. 删除镜像 ： docker rmi ID

## 容器操作

| 操作     | 命令                                                         | 说明                                                         |
| -------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 运行     | docker run --name container-name -d imager_name              | --name:自定义的容器名称；-d:后台运行；image——name：指定镜像模板 |
| 列表     | docker ps                                                    | 加个-a 查看所有容器                                          |
| 停止     | docker stop container-name/id                                |                                                              |
| 启动     | docker start container-name/id                               |                                                              |
| 删除     | doceker rn=m id                                              |                                                              |
| 端口映射 | -p \*\*\*\*:\*\*\*\*   例如：docker run --name container-name -d -p 8080:8080 imager_name | 启动时候添加-p 主机端口（映射到）容器内部端口 主机端口：容器内部端口 |
| 容器日志 | docker logs containerID/Name                                 |                                                              |

#### 正确启动mysql

```shell
docker run --name mysql1-e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag
```

#### 启动es

```bash
docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discoverytype =single-node" elasticseach
```

### 打包镜像

1. 写Dockerfile

   ```ba
   FROM centos
   
   COPY redme.txt /usr/local/readme/txt
    ...
   ```

2. 使用命令

   > docker build -t yourmirrorname .

### docker网络

每启动一个docker容器，docker为每个容器发布一个ip地址，使用的桥接模式，使用的技术是evth-pair技术（把docker容器里面的网卡映射到物理机，一对一对的网卡）

容器之间是可以ping通的

![image-20200826162449075](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200826162449075.png)

#### --link

通过容器名来联通

在run的时候加入`--link`+`另一个容器名`可以把2个联通

本质是一个hosts中配置增加了一个映射

###  自定义一个网络

```shell
docker network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.108.0.1 mynet
```

+ --driver bridge :设置桥接
+  --subnet 192.168.0.0/16 ：设置子网
+ -gateway 192.108.0.1 ：设置网关

之后启动容器时候加上 --net mynet就可以了

```bash
docker run -d -P --name tomcat01 --net mynet to 
```

