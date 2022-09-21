#### docker-compose.yml

```yaml
version: "3.0"
networks:
  container:
    driver: bridge
volumes:
  esconfig:
  esdata:
services:
  mysql:
    image: mysql:8.0.26
    container_name: mysql
    ports:
      - "3306:3306"
    volumes:
      - "./mysql/data:/var/lib/mysql"
      - "./mysql/conf.d:/etc/mysql/conf.d"
      - "/etc/localtime:/etc/localtime:ro"
    environment:
      - "MYSQL_ROOT_PASSWORD=ljzyou2513"
      - "TZ=Asia/Shanghai"
      - "SET_CONTAINER_TIMEZONE=true"
      - "CONTAINER_TIMEZONE=Asia/Shanghai"
    restart: always
    networks:
      container:

  redis:
    image: redis:6.2.6
    privileged: true
    container_name: redis
    volumes:
      - "./redis/data:/data:rw"
      - "./redis/redis.conf:/usr/local/etc/redis/redis.conf:rw"
    restart: always
    command: redis-server /usr/local/etc/redis/redis.conf --requirepass "ljzyou2513"
    ports:
      - "6379:6379"
    environment:
      - "TZ=Asia/Shanghai"
      - "LANG=en_US.UTF-8"
    networks:
      container:

  nginx:
    image: nginx:1.18.0
    container_name: nginx
    restart: always
    hostname: nginx
    privileged: true
    ports:
      - "80:80"
    volumes:
      - "./nginx/logs:/var/log/nginx/"
      - "./nginx/nginx.conf:/etc/nginx/nginx.conf:rw"
      - "./nginx/html:/usr/share/nginx/html/"
    networks:
      container:

  nacos:
    image: nacos/nacos-server:v1.4.3
    container_name: nacos
    restart: always
    privileged: true
    ports:
      - "8848:8848"
    depends_on:
      - mysql
    volumes:
      - "./nacos/data:/home/nacos/data"
      - "./nacos/plugins:/home/nacos/plugins"
      - "./nacos/application.properties:/home/nacos/conf/application.properties"
    environment:
      - "MODE=standalone"
      - "JVM_XMS=256m"
      - "JVM_MMS=256m"
    networks:
      container:

  rabbitmq:
    image: rabbitmq:3.8.30
    container_name: rabbitmq
    restart: always
    volumes:
      - "./rabbitmq/data:/var/lib/rabbitmq/"
    ports:
      - "5672:5672"
      - "15672:15672"
    networks:
      container:

#  rocketmq-namesrv:
#    image: foxiswho/rocketmq:4.8.0
#    container_name: rocketmq-namesrv
#    restart: always
#    ports:
#      - 9876:9876
#    volumes:
#      # ./namesrv/logs 主机路径（docker-compose.yml的相对路径）：/home/rocketmq/logs 容器内路径
#      - "./rocketmq/logs:/home/rocketmq/logs"
#      - "./rocketmq/namesrv/store:/home/rocketmq/store"
#    environment:
#      JAVA_OPT_EXT: "-Duser.home=/home/rocketmq -Xms256M -Xmx256M -Xmn128m"
#    command: ["sh","mqnamesrv"]
#    networks:
#      container:
#
#
#  rocketmq-broker:
#    image: foxiswho/rocketmq:4.8.0
#    container_name: rocketmq-broker
#    restart: always
#    ports:
#      - "10909:10909"
#      - "10911:10911"
#    volumes:
#      - "./rocketmq/logs:/home/rocketmq/logs"
#      - "./rocketmq/store:/home/rocketmq/store"
#      - "./rocketmq/conf:/etc/rocketmq/broker.conf:rw"
#    environment:
#      JAVA_OPT_EXT: "-Duser.home=/home/rocketmq -Xms256M -Xmx256M -Xmn128m"
#      # 容器内路径
#    command: ["sh","mqbroker","-c","/etc/rocketmq/broker.conf","-n","rocketmq-namesrv:9876","autoCreateTopicEnable=true"]
#    depends_on:
#      - rocketmq-namesrv
#    networks:
#      container:
#
#  rocketmq-console:
#    image: styletang/rocketmq-console-ng
#    container_name: rocketmq-console
#    restart: always
#    ports:
#      - "10912:8080"
#    environment:
#      JAVA_OPTS: "-Drocketmq.namesrv.addr=rocketmq-namesrv:9876 -Dcom.rocketmq.sendMessageWithVIPChannel=false"
#    depends_on:
#      - rocketmq-namesrv
#    networks:
#      container:

  elasticsearch:
    image: elasticsearch:7.14.0
    container_name: elasticsearch
    volumes:
      - "esdata:/usr/share/elasticsearch/data"
      - "esconfig:/usr/share/elasticsearch/config"
      - "./elasticsearch/ik-7.14.0:/usr/share/elasticsearch/plugins/ik-7.14.0"
    ports:
      - "9200:9200"
      - "9300:9300"
    environment:
      - "discovery.type=single-node"
      - "ES_JAVA_OPTS=-Xms256m -Xmx256m"
    restart: always
    privileged: true
    networks:
      container:

  ibana:
   image: kibana:7.14.0
   container_name: kibana
   volumes:
     - "./kibana/kibana.yml:/usr/share/kibana/config/kibana.yml"
   ports:
     - "5601:5601"
   restart: always
   privileged: true
   depends_on:
     - elasticsearch
   networks:
     container:

  portainer:
    image: portainer/portainer:latest
    container_name: portainer
    ports:
      - "9000:9000"
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock"
    privileged: true
    restart: always
    networks:
      container:

  tracker:
    image: season/fastdfs:1.2
    container_name: tracker
    restart: always
    volumes:
      - "./tracker_data:/fastdfs/tracker/data"
    ports:
      - "22122:22122"
    command: "tracker"
    networks:
      container:

  storage:
    image: season/fastdfs:1.2
    container_name: storage
    links:
      - tracker
    restart: always
    volumes:
      - "./storage.conf:/fdfs_conf/storage.conf"
      - "./storage_base_path:/fastdfs/storage/data"
      - "./store_path0:/fastdfs/store_path"
    ports:
      - "23000:23000"
    environment:
      TRACKER_SERVER: "192.168.75.128:22122"
    command: "storage"
    networks:
      container:

  nginx1:
    image: season/fastdfs:1.2
    container_name: fdfs-nginx
    restart: always
    volumes:
      - "./nginx.conf:/etc/nginx/conf/nginx.conf"
      - "./store_path0:/fastdfs/store_path"
    links:
      - tracker
    ports:
      - "8088:8088"
    environment:
      TRACKER_SERVER: "192.168.75.128:22122"
    command: "nginx"
    networks:
      container:
```

#### redis.conf

```conf
# 修改连接为所有ip
bind 0.0.0.0
# 允许外网访问
protected-mode no
port 6379
timeout 0
# RDB存储配置
save 900 1
save 300 10
save 60 10000
rdbcompression yes
dbfilename dump.rdb
# 数据存放位置
dir /data
# 开启aof配置
appendonly yes
appendfsync everysec
appendfilename "appendonly.aof"

auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb
# 设置密码
requirepass ljzyou2513
masterauth "ljzyou2513"
```

#### nginx.conf

```conf

#user  nobody;
worker_processes  1;

error_log  /var/log/nginx/error.log notice;
pid        /var/run/nginx.pid;
#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;
    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;
    include /etc/nginx/conf.d/*.conf;

    server {
        listen       80;
        server_name  localhost;
        charset utf-8;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   /usr/share/nginx/html/;
            try_files $uri $uri/ =404;
            index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}
```

#### nacos的application.properties

```properties
#
# Copyright 1999-2018 Alibaba Group Holding Ltd.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#

#*************** Spring Boot Related Configurations ***************#
### Default web context path:
server.servlet.contextPath=/nacos
### Default web server port:
server.port=8848

#*************** Network Related Configurations ***************#
### If prefer hostname over ip for Nacos server addresses in cluster.conf:
# nacos.inetutils.prefer-hostname-over-ip=false

### Specify local server's IP:
# nacos.inetutils.ip-address=


#*************** Config Module Related Configurations ***************#
### If use MySQL as datasource:
spring.datasource.platform=mysql

### Count of DB:
db.num=1

### Connect URL of DB:
db.url=jdbc:mysql://192.168.75.128:3306/nacos?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useUnicode=true&useSSL=false&serverTimezone=UTC
db.user=root
db.password=ljzyou2513

### Connection pool configuration: hikariCP
db.pool.config.connectionTimeout=30000
db.pool.config.validationTimeout=10000
db.pool.config.maximumPoolSize=20
db.pool.config.minimumIdle=2

#*************** Naming Module Related Configurations ***************#
### Data dispatch task execution period in milliseconds:
# nacos.naming.distro.taskDispatchPeriod=200

### Data count of batch sync task:
# nacos.naming.distro.batchSyncKeyCount=1000

### Retry delay in milliseconds if sync task failed:
# nacos.naming.distro.syncRetryDelay=5000

### If enable data warmup. If set to false, the server would accept request without local data preparation:
# nacos.naming.data.warmup=true

### If enable the instance auto expiration, kind like of health check of instance:
# nacos.naming.expireInstance=true

nacos.naming.empty-service.auto-clean=true
nacos.naming.empty-service.clean.initial-delay-ms=50000
nacos.naming.empty-service.clean.period-time-ms=30000


#*************** CMDB Module Related Configurations ***************#
### The interval to dump external CMDB in seconds:
# nacos.cmdb.dumpTaskInterval=3600

### The interval of polling data change event in seconds:
# nacos.cmdb.eventTaskInterval=10

### The interval of loading labels in seconds:
# nacos.cmdb.labelTaskInterval=300

### If turn on data loading task:
# nacos.cmdb.loadDataAtStart=false


#*************** Metrics Related Configurations ***************#
### Metrics for prometheus
#management.endpoints.web.exposure.include=*

### Metrics for elastic search
management.metrics.export.elastic.enabled=false
#management.metrics.export.elastic.host=http://localhost:9200

### Metrics for influx
management.metrics.export.influx.enabled=false
#management.metrics.export.influx.db=springboot
#management.metrics.export.influx.uri=http://localhost:8086
#management.metrics.export.influx.auto-create-db=true
#management.metrics.export.influx.consistency=one
#management.metrics.export.influx.compressed=true


#*************** Access Log Related Configurations ***************#
### If turn on the access log:
server.tomcat.accesslog.enabled=true

### The access log pattern:
server.tomcat.accesslog.pattern=%h %l %u %t "%r" %s %b %D %{User-Agent}i %{Request-Source}i

### The directory of access log:
server.tomcat.basedir=


#*************** Access Control Related Configurations ***************#
### If enable spring security, this option is deprecated in 1.2.0:
#spring.security.enabled=false

### The ignore urls of auth, is deprecated in 1.2.0:
nacos.security.ignore.urls=/,/error,/**/*.css,/**/*.js,/**/*.html,/**/*.map,/**/*.svg,/**/*.png,/**/*.ico,/console-ui/public/**,/v1/auth/**,/v1/console/health/**,/actuator/**,/v1/console/server/**

### The auth system to use, currently only 'nacos' is supported:
nacos.core.auth.system.type=nacos

### If turn on auth system:
nacos.core.auth.enabled=false

### The token expiration in seconds:
nacos.core.auth.default.token.expire.seconds=18000

### The default token:
nacos.core.auth.default.token.secret.key=SecretKey012345678901234567890123456789012345678901234567890123456789

### Turn on/off caching of auth information. By turning on this switch, the update of auth information would have a 15 seconds delay.
nacos.core.auth.caching.enabled=true

### Since 1.4.1, Turn on/off white auth for user-agent: nacos-server, only for upgrade from old version.
nacos.core.auth.enable.userAgentAuthWhite=true

### Since 1.4.1, worked when nacos.core.auth.enabled=true and nacos.core.auth.enable.userAgentAuthWhite=false.
### The two properties is the white list for auth and used by identity the request from other server.
nacos.core.auth.server.identity.key=
nacos.core.auth.server.identity.value=

#*************** Istio Related Configurations ***************#
### If turn on the MCP server:
nacos.istio.mcp.server.enabled=false



###*************** Add from 1.3.0 ***************###


#*************** Core Related Configurations ***************#

### set the WorkerID manually
# nacos.core.snowflake.worker-id=

### Member-MetaData
# nacos.core.member.meta.site=
# nacos.core.member.meta.adweight=
# nacos.core.member.meta.weight=

### MemberLookup
### Addressing pattern category, If set, the priority is highest
# nacos.core.member.lookup.type=[file,address-server]
## Set the cluster list with a configuration file or command-line argument
# nacos.member.list=192.168.16.101:8847?raft_port=8807,192.168.16.101?raft_port=8808,192.168.16.101:8849?raft_port=8809
## for AddressServerMemberLookup
# Maximum number of retries to query the address server upon initialization
# nacos.core.address-server.retry=5
## Server domain name address of [address-server] mode
# address.server.domain=jmenv.tbsite.net
## Server port of [address-server] mode
# address.server.port=8080
## Request address of [address-server] mode
# address.server.url=/nacos/serverlist

#*************** JRaft Related Configurations ***************#

### Sets the Raft cluster election timeout, default value is 5 second
# nacos.core.protocol.raft.data.election_timeout_ms=5000
### Sets the amount of time the Raft snapshot will execute periodically, default is 30 minute
# nacos.core.protocol.raft.data.snapshot_interval_secs=30
### raft internal worker threads
# nacos.core.protocol.raft.data.core_thread_num=8
### Number of threads required for raft business request processing
# nacos.core.protocol.raft.data.cli_service_thread_num=4
### raft linear read strategy. Safe linear reads are used by default, that is, the Leader tenure is confirmed by heartbeat
# nacos.core.protocol.raft.data.read_index_type=ReadOnlySafe
### rpc request timeout, default 5 seconds
# nacos.core.protocol.raft.data.rpc_request_timeout_ms=5000
```

#### kibana.yml

```yaml
server.port: 5601
# kibana的主机地址 0.0.0.0可表示监听所有IP
server.host: "0"
server.shutdownTimeout: "5s"
# kibana访问es的URL
elasticsearch.hosts: ["http://elasticsearch:9200"]
monitoring.ui.container.elasticsearch.enabled: true
```

