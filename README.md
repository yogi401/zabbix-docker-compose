## docker-compose 一键部署zabbix-server,zabbix-web,zabbix-agent,连接外部数据库mysql
基于官方文档，增加了固定IP配置

```
zabbix-server：172.16.239.4  
zabbix-web-apache-mysql:172.16.239.3  
zabbix-server-agent: 172.16.239.5  
#mysql-server: 172.20.1.53 
```

### 路径说明
zabbix-web docker build将字体文件拷贝到zabbix-web-apache-mysql中重新构建镜像，修复中文乱码的问题。  
zbx_env zabbix挂载路径  
common/env 环境配置文件目录  


### 网络配置
zbx\_net\_backend: 内部访问使用，避免将zabbix-server与agent，web之间的访问暴露到外网，同时将zabbix-server和zabbix-agent内部地址固定，方便在页面添加配置agent，地址段172.16.239.0/24  
zbx\_net\_frontend: web外部访问使用,地址段172.16.238.0/24


### 环境配置文件
路径：zabbix-server/common/env/

zabbix-server: env_server  
  
```
DB_SERVER_HOST=172.20.1.53   #对应mysql地址
MYSQL_DATABASE=zabbix  
MYSQL_USER=zabbix
MYSQL_PASSWORD=zabbix.server
MYSQL_ROOT_PASSWORD=rootpassword
ZBX_LOGTYPE=file
ZBX_LOG_FILE=/tmp/ser.log
```

zabbix-web-apache-mysql: env_web  
  
```
DB_SERVER_HOST=172.20.1.53  #对应mysql地址
MYSQL_DATABASE=zabbix
MYSQL_USER=zabbix
MYSQL_PASSWORD=zabbix.server
MYSQL_ROOT_PASSWORD=rootpassword
ZBX_SERVER_HOST=zabbix-server
PHP_TZ=Asia/Shanghai
```

zabbix-server-agent: env_agent  
  
```
ZBX_SERVER_HOST=172.16.239.4 #对应zabbix-server地址
ZBX_SERVER_PORT=10051
ZBX_LISTENPORT=10050
```

### 使用方法  

```
mkdir ~/zabbix-server
cd ~/zabbix-server
git clone https://github.com/yogi401/zabbix-server.git
cd zabbix-server
docker-compose --build
docker-compose up -d
```

