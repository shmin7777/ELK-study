# ELK-study
Elastic Stack study



# 강의자료
https://elastic-korea.gitbook.io/elastic-reference-box/undefined-7

https://esbook.kimjmin.net/

# docker 실행
```  
docker run -d -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" --name elasticsearch7 docker.elastic.co/elasticsearch/elasticsearch:7.9.1

docker run -d --link elasticsearch7:elasticsearch -p 5601:5601 --name kibana7 docker.elastic.co/kibana/kibana:7.9.1
```
