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
