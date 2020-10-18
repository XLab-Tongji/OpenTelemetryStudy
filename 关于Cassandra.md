### 创建Cassandra集群

容器ID：a6d28e4a9ec4

###### 拉取镜像

```
docker pull cassandra
```

###### 新建目录 
```
mkdir -p /opt/cassandra
```
`/opt/cassandra`的结构如下：

```
./
├── cassandra.yaml
└── docker-compose.yaml
```

###### 后台运行容器

```
$ docker run -itd cassandra /bin/bash
```

###### 拷贝配置文件
```
cd opt/cassandra
docker cp a6d28e4a9ec4:/opt/cassandra/conf/cassandra.yaml .
```
vim修改`cassandra.yaml`，将

```
authenticator: AllowAllAuthenticator
```

修改为：

```
authenticator: PasswordAuthenticator
```

这样做的目的是为了，可以使用用户名和密码，进行远程连接。默认的策略，好像只能本地连接。

将`docker-compose.yaml`放入与`opt/cassandra`下，文件内容如下：

###### docker-compose.yaml

```
version: '3'
services:
  cassandra-1:
    image: cassandra
    container_name: cassandra-1
    volumes:
      - ./cassandra.yaml:/opt/cassandra/conf/cassandra.yaml
      - /xuhaiqi/cassandra-cluster/cassandra-1/cassandra:/var/lib/cassandra
    environment:
      - CASSANDRA_BROADCAST_ADDRESS=cassandra-1
    ports:
      - "7000:7000"
      - "9042:9042"
    restart: always
  cassandra-2:
    image: cassandra
    container_name: cassandra-2
    volumes:
      - ./cassandra.yaml:/opt/cassandra/conf/cassandra.yaml
      - /xuhaiqi/cassandra-cluster/cassandra-2/cassandra:/var/lib/cassandra
    environment:
      - CASSANDRA_BROADCAST_ADDRESS=cassandra-2
      - CASSANDRA_SEEDS=cassandra-1
    ports:
      - "7001:7000"
      - "9043:9042"
    depends_on:
      - cassandra-1
    restart: always
  cassandra-3:
    image: cassandra
    container_name: cassandra-3
    volumes:
      - ./cassandra.yaml:/opt/cassandra/conf/cassandra.yaml
      - /xuhaiqi/cassandra-cluster/cassandra-3/cassandra:/var/lib/cassandra
    environment:
      - CASSANDRA_BROADCAST_ADDRESS=cassandra-3
      - CASSANDRA_SEEDS=cassandra-1
    ports:
      - "7002:7000"
      - "9044:9042"
    depends_on:
      - cassandra-2
    restart: always
```

说明：

cassandra.yaml 挂载到容器中，开启用户远程登录。

/xuhaiqi/cassandra-cluster 是本地目录，用来做持久化的。

CASSANDRA_BROADCAST_ADDRESS 此变量用于控制向其他节点播发哪个IP地址。

CASSANDRA_SEEDS 这个变量是用逗号分隔的IP地址列表，gossip 用来引导加入集群的新节点。

###### 创建持久化目录

```
mkdir -p xuhaiqi/cassandra-cluster/cassandra-{1,2,3}
```

#### 启动docker-compose

【在容器未停止运行的情况下，可直接执行以下命令启动Cassandra】

可以利用 docker-compose 命令来启动Cassandra集群：

```
cd opt/cassandra
docker-compose up -d
```

启动之后，需要等待1分钟左右。

这个时候，如果使用docker logs命令查看日志，会发现它会有一些报错，不必理会

因为把数据目录映射了出来，默认是空的。所以第一次启动时，会报错。不过没有关系，docker会自动重新启动几次。

在第3次时，就会启动成功了。

###### 查看容器运行状态

```
# docker-compose ps
```

###### 查看Cassandra集群状态

使用Cassandra自带命令：

```
# docker exec -ti cassandra-1 cqlsh -u cassandra -pcassandra cassandra-2 -e "DESCRIBE CLUSTER"
```

显示结果：

Cluster: Test Cluster
Partitioner: Murmur3Partitioner

由于开启了密码认证。连接cassandra时，需要用户名和密码。这里的-u参数指定用户名，-p指定密码。

默认的用户名和密码都是cassandra

### 数据库的使用

默认情况下，Cassandra提供一个提示Cassandra查询语言shell（cqlsh），允许用户与它通信。使用此shell，可以执行Cassandra查询语言（CQL）。

使用cqlsh，可以：

- 定义模式，
- 插入数据，
- 执行查询。

###### 启动cqlsh

```
# docker exec -it cassandra-1 /bin/bash
# cqlsh -u cassandra -pcassandra
```

###### 查询keyspaces

列出来的相当于关系型数据的的系统数据库名

```
cassandra@cqlsh> describe keyspaces;

system_traces  system_schema  system_auth  system  system_distributed
```

###### 创建数据库

```
cassandra@cqlsh> CREATE KEYSPACE IF NOT EXISTS mycasdb WITH REPLICATION = {'class': 'SimpleStrategy','replication_factor':3};
cassandra@cqlsh> describe keyspaces;

system_schema  system_auth  mycasdb  system  system_distributed  system_traces
```

Notes：

Replication Factor : 复制因数。 表示一份数据在一个DC 之中包含几份。常用奇数, 比如上述设置的replication_factor=3

Replica placement strategy : 复制策略。 默认的是SimpleStrategy 。如果是单机架、单数据中心的模式，保持使用SimpleStrtegy即可。

###### 创建表

在mycasdb数据库中创建一个表，首先使用use mycasdb；表示要使用此数据库，然后再使用：

```
cassandra@cqlsh> use mycasdb;
cassandra@cqlsh:mycasdb> CREATE TABLE user (id int,user_name varchar,PRIMARY KEY (id));
```

###### 查看表

查看数据库中的表

```
cassandra@cqlsh:mycasdb> describe tables;

user
```

###### 插入表数据

向user表中插入输入，使用：

```
cassandra@cqlsh:mycasdb> INSERT INTO user (id,user_name) VALUES (1,'xhq1');
cassandra@cqlsh:mycasdb> INSERT INTO user (id,user_name) VALUES (2,'xhq12');
cassandra@cqlsh:mycasdb> INSERT INTO user (id,user_name) VALUES (3,'xhq123');
cassandra@cqlsh:mycasdb> INSERT INTO user (id,user_name) VALUES (4,'xhq1234');
```

###### 查询表数据

```
cassandra@cqlsh:mycasdb> select * from user;

 id | user_name
----+-----------
  1 |       xhq1
  2 |      xhq12
  4 |    xhq1234
  3 |     xhq123

(4 rows)
```

### 可视化工具调研

[官方介绍](https://cwiki.apache.org/confluence/display/CASSANDRA2/Administration+Tools)

Cassandra提供了用于访问和管理Cassandra的命令行工具：cqlsh和nodetool。

下面是第三方的图形工具的简单介绍：

##### tableplus

[下载](https://tableplus.com/?spm=a2c4e.10696291.0.0.6c3119a4wtinHQ)    有Mac版本和Win版本。免费版本基本够用，高级功能需要收费。

【为了免于用命令行写SQL以及方便操作表的话，这个最实用，但是它不能监测集群的指标和性能】

点击右下角create a new connection 选择 Cassandra，填写user, password, host, port等信息后，点击Test，出现OK后，即连接成功。

接下来就是创建mycasdb -> 写sql语句 -> 点击Run 就会出现结果了。

##### OpsCenter

[中文用户手册](https://blog.csdn.net/achejq/article/details/51259188?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522160303414719195188310383%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=160303414719195188310383&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_v2~rank_v28-4-51259188.first_rank_ecpm_v3_pc_rank_v2&utm_term=OpsCenter&spm=1018.2118.3001.4187)

OpsCenter是一个用于管理和监视Cassandra集群的工具，以Dashboard的方式展示指标。

有免费下载和使用OpsCenter的社区版。

DataStaxOpsCenter是一个可视化管理和监控Apache Cassandra和DataStax Enterprise工具。OpsCenter简化了管理任务：

- 添加扩展集群
- 配置nodes
- 查看性能指标
- 修复问题
- 监控集群情况

##### Helenos

[用户指南](http://www.helenos.org/wiki/UsersGuide)

Helenos是一个基于web的GUI工具，可以帮助探索数据和管理模式。

它将关键操作系统功能（如文件系统，网络，设备驱动程序和图形用户界面）分解为一系列细粒度的用户空间组件，这些组件通过消息传递相互交互。一个组件的故障或崩溃不会直接损害其他组件。因此，HelenOS具有灵活性，模块化，可扩展性，容错性和易于理解性。

HelenOS是开源的免费软件。其源代码可在BSD许可下获得。某些第三方组件是根据GPL许可的。

##### SPM

用于Cassandra性能监视、警报和异常检测的SPM，监视所有Cassandra指标，以及许多OS和JVM指标。它支持自定义仪表板，订阅电子邮件，图表嵌入，过滤服务器，节点等。除了监视Cassandra, SPM还可以监视Solr、Hadoop、Spark、Kafka、Storm、ZooKeeper、Elasticsearch、Java webapps和通用Java应用程序，以及许多其他类型的应用程序，以及自定义指标。

##### 

