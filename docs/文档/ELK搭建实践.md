1. docker环境准备
   docker version:  
   ```
   Docker version 20.10.17, build 100c701
   ```
   docker-compose version:
   ```
   Docker Compose version v2.10.2
   ```

2. 新建elk目录,在elk目录下新建docker-compose.yml文件
   ```
    version: '3'
    services:
    elasticsearch:
        image: docker.elastic.co/elasticsearch/elasticsearch:7.15.0
        container_name: elasticsearch_server
        restart: always 
        environment:
        - discovery.type=single-node
        - discovery.zen.minimum_master_nodes=1
        - ES_JAVA_OPTS=-Xms3g -Xmx3g
        volumes:
        - ./elasticsearch/data:/usr/share/elasticsearch/data
        ports:
        - 9200:9200
        - 9300:9300 
        networks:
        elk_net:     # 指定使用的网络
            aliases:
            - elasticsearch     # 该容器的别名，在 elk_net 网络中的其他容器可以通过别名 elasticsearch 来访问到该容器

    kibana:
        image: docker.elastic.co/kibana/kibana:7.15.0
        container_name: kibana_server
        ports:
        - "5601:5601"
        restart: always
        networks:
        elk_net:
            aliases:
            - kibana
        environment:
        - ELASTICSEARCH_URL=http://elasticsearch:9200
        - SERVER_NAME=kibana
        # 如需具体配置，可以创建./config/kibana.yml，并映射
        # volumes:
        #   - ./config/kibana.yml:/usr/share/kibana/config/kibana.yml
        depends_on:
        - elasticsearch

    logstash:
        image: docker.elastic.co/logstash/logstash:7.15.0
        container_name: logstash_server
        restart: always
        environment:
        - LS_JAVA_OPTS=-Xmx256m -Xms256m
        volumes:
        - ./config/logstash.conf:/etc/logstash/conf.d/logstash.conf
        networks:
        elk_net:
            aliases:
            - logstash
        depends_on:
        - elasticsearch
        entrypoint:
        - logstash
        - -f
        - /etc/logstash/conf.d/logstash.conf
        logging:
        driver: "json-file"
        options:
            max-size: "200m"
            max-file: "3"
        ports:
        - 4560:4560

    networks:
    elk_net:
        external:
        name: elk_net
   ```

3. 在elk目录下新建config文件夹,在elk/config目录下新建文件elasticsearch.yml

   ```yaml
   cluster.name: "docker-cluster"
   network.host: 0.0.0.0
   http.cors.enabled: true
   http.cors.allow-origin: "*"
   http.cors.allow-headers: Authorization

   ```

4.在elk/config目录下新建文件kibana.yml

```yaml
server.host: "0.0.0.0"
server.shutdownTimeout: "5s"
elasticsearch.hosts: [ "http://elasticsearch:9200" ]
monitoring.ui.container.elasticsearch.enabled: true
```

5.在elk/config目录下新建文件logstash.yml

```yaml
http.host: "0.0.0.0"
xpack.monitoring.elasticsearch.hosts: [ "http://elasticsearch:9200" ]
xpack.monitoring.enabled: true
```

新建文件logstash.conf

```yaml
input {
    tcp {
    port => 4560
    codec => json_lines
    }
}
output {
    elasticsearch {
    hosts => ["http://elasticsearch:9200"]
index => "test-logstash"
    }
}
```

logstash.conf表示logstash监听5460端口,并接受日志.同时推送到elasticsearch服务中.

6.返回docker-composet.yml目录所在层级,并执行

```sh
docker-compose up -d
```

至此,elk服务启动完成.

7.修改应用服务,让日志输入logstash,以Spring Boot中框架距离,在logback-spring.xml配置文件中新增如下配置:

```xml
<appender name="logstash" class="net.logstash.logback.appender.LogstashTcpSocketAppender">
<destination>localhost:5000</destination>
    <encoder class="net.logstash.logback.encoder.LogstashEncoder"/>
</appender>
```

这样,在kibana中就可以看到Spring Boot项目运行过程中的日志.

______________________________

源码在./src/.elk目录下

