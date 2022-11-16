# 단일 node docker compose
```
version: '3'

services:
  elasticsearch:
    container_name: elasticsearch
    image: elasticsearch:7.9.0
    ports:
     - 9200:9200
     - 9300:9300
    networks:
     - default
    volumes:
     - /usr/share/data/elasticsearch:/usr/share/elasticsearch/data
    environment:
     - discovery.type=single-node
     - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
     - TZ=Asia/Seoul
    user: root
    restart: always
    privileged: true
```  

# local cluster docker compose
```  
version: '2'
services:
  es01:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.10.1
    container_name: es01
    environment:
      - node.name=es01
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es02,es03
      - cluster.initial_master_nodes=es01,es02,es03
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data01:/usr/share/elasticsearch/data
    ports:
      - 9200:9200
      - 9300:9300
    networks:
      - elastic
  es02:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.10.1
    container_name: es02
    environment:
      - node.name=es02
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es03
      - cluster.initial_master_nodes=es01,es02,es03
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data02:/usr/share/elasticsearch/data
    ports:
      - 9301:9300
    networks:
      - elastic
  es03:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.10.1
    container_name: es03
    environment:
      - node.name=es03
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es02
      - cluster.initial_master_nodes=es01,es02,es03
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data03:/usr/share/elasticsearch/data
    ports:
      - 9302:9300
    networks:
      - elastic
  kibana:
    container_name: kibana
    image: docker.elastic.co/kibana/kibana:7.10.1
    environment:
      SERVER_NAME: kibana
      ELASTICSEARCH_HOSTS: http://es01:9200
    ports:
      - 5601:5601
    depends_on:
      - es01
      - es02
      - es03
    networks:
      - elastic

volumes:
  data01:
    driver: local
  data02:
    driver: local
  data03:
    driver: local
  kibana:
    driver: local

networks:
  elastic:
    driver: bridge
```


# 각 instance에 run
```  
docker run -d \
       --name elasticsearch \
       -p 9200:9200 -p 9300:9300 \
       -e ES_JAVA_OPTS="-Xms2g -Xmx2g" \
       -v `pwd`/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml \
       docker.elastic.co/elasticsearch/elasticsearch:8.0.1
       
 # 잘 구동 되었는지 확인      
 curl -XGET http://localhost:9200/          
 
 curl -XGET http://localhost:9200/_cat/nodes?v=true
```  

#  elasticsearch.yml 
```  
cluster.name: "es-cluster"  # 동일하게 설정
node.name: 노드 이름
node.roles: ["master"]  # 데이터 노드는 ["data"]
bootstrap.memory_lock: true
network.host: _site_
network.publish_host: 각 인스턴스의 IP # docker 로 실행했을 경우 필수
discovery.seed_hosts: [클러스터링할 노드의 IP, ... ]
cluster.initial_master_nodes: [마스터 노드 리스트, ...]
xpack.license.self_generated.type: trial
xpack.security.enabled: false
```  


node 1 : es 2개,  node 2: es 1개로 클러스터링함.  
![image](https://user-images.githubusercontent.com/67637716/202222075-e193f098-e87a-4908-9118-dbcba7f05056.png)  






https://velog.io/@miintto/es-cluster  
