**kafka overview**
Topic Producers
message consumers
broker (cluster)

First steps in rebuilding sensor
first step with sensor is to install centos 
qallocate partition
create user account and make user admin as the root user
configure IP addr
if forgeot to set in the GUI, it can be done in the network script
setup repo



# week 2
Day 6

# Installing Elasticsearch

1. ssh into 172.16.10.100
2.sudo yum install Elasticsearch-7.8.1
3. sudo chown elasticsearch: /data/elasticsearch  
4. sudo vi /etc/elasticsearch/elasticsearch.yml
5. remove comment 17 and update it to sg10-cluster
6. remove comment 23 and update sg10-node
line 33: /data/elasticsearch
unccoment line 43
uncomment 55 and update _local:ipv4_
line 59 uncomment
line 68:uncomment and clear it out and udpate to [172.16.10.100]
uncomment line 72 and update to [172.16.10.100]
discvoery type: rename it to single-node
comment out: discovery seed host and cluster initial master node
:wq

Create Service Directory
1. sudo mkdir -p /usr/lib/systemd/system/elasticsearch.service.d

sudo vi /usr/lib/systemd/system/elasticsearch.service.d/override.conf

[Service]
LimitMEMLOCK=infinity
:wq

sudo chmod 755 /usr/lib/systemd/system/elasticsearch.service.d
sudo chmod 644 /usr/lib/systemd/system/elasticsearch.service.d/override.conf

sudo systemctl daemon-reload

sudo systemctl start elasticsearch
sudo systemctl status elasticsearch 
sudo systemctl enable elasticsearch 

sudo vi /etc/elasticsearch/jvm.options
:set nu
line 22 and line 23. changing Xms1g to Xms4g
:wq

sudo systemctl restart elasticsearch

ss -lnt
sudo firewall-cmd --add-port={9200,9300}/tcp --permanent
sudo fireall-cmd --reload
curl localhost:9200
curl localhost:9200/_cat/nodes?v


# installing kibana 
gain insight into the data inside Elasticsearch 

sudo yum install kibana-7.8.1
sudo vi /etc/kibana.yml

uncomment and change to line 7: server.host: "172.16.10.100" 
uncomment line 28
sudo systemctl start kibana
sudo systemctl status kibana
sudo systemctl enable kibana

sudo firewall-cmd --add-port=5601/tcp --permanent
sudo firewall-cmd --reload

172.16.10.100:5601

cd ~
curl -LO http://192.168.2.20:8080/ecskibana.tar.gz
ll
tar -zxvf ecskibana.tar.gz
cd  ecskibana
./import-index-templates.sh

go dev tool under management in kibana
GET _cat/templates?v and then run it
GET _cat/templates/v&s=name

go stack management and then index pattern


***********************issue here
sudo cd /etc/filebeat
sudo yum install filebeat-7.8.1
sudo mv /etc/filebeat/filebeat.yml /etc/filebeat/filebeat.yml.bk
sudo curl -LO http://192.168.2.20:8080/filebeat.yml

sudo vi filebeat.yml
add: 
- type: log
  enabled: true
  paths:
   - /data/fsf/logs/rockout.log
  json.keys_under_root: true
  fields:
    kafka_topic:fsf-raw
  fileds_under_root: true
  sudo systemctl start filebeat
  sudo systemctl status filebeat
  sudo systemctl enable filebeat

  sud0 /usr/share/kafka/bin/kafka-topics.sh --bootstrap-server 172.16.10.100:9092 --list

sudo vi filebeat.yml 
for host in output.kafka..put in 172.16.10.100 for localhost

sudo /usr/share/kafka/bin/kafka-console-consumer.sh --boootstrap-server 172.16.10.100:9092 --topic suricata-raw --from-beginning

sudo /usr/share/kafka/bin/kafka-console-consumer.sh --boootstrap-server 172.16.10.100:9092 --topic fsf-raw --from-beginning



# Logstash
sudo yum install logstash-8.7.1-1
cd /etc/logstash/conf.d/
ll
sudo curl -LO http://192.168.2.20:8080/logstash.tar
ll
sudo tar -xf logstash.tar
ll
cd conf.d/
sudo vi logstash-100-input-kafka-zeek.conf




sudo curl -LO http://192.168.2.20:8080/logstash.tar
sudo tar -xvf logstash.tar
cd conf.d
sudo vi logstash-100-input-kafka-zeek.conf
change ip (172.16.10.100) in bootstrap server line 

sudo vi logstash-100-input-kafka-suricata.conf
change ip (172.16.10.100) in bootstrap server line 

sudo vi logstash-100-input-kafka-fsf.conf
change ip (172.16.10.100) in bootstrap server line 

sudo vi logstash-9999-output-elasticsearch.conf
:set nu
:%s/127.0.0.1/172.16.10.100
commenting out line 2
:wq

sudo systemctl start logstash
sudo systemctl status logstash

sudo /etc/elasticsearch/elasticsearch.yml
change line 55.: network_host to _eno1_
sudo systemctl restart elasticsearch

tail /var/log/lostash/logstash-plain.log

log into elasticsearch 172.16.10.100:5601
index patterns (kibana)
ecs-*
ecs-suricata-*
step 2:@timestamp

ecs-zeek-*


sudo vi /etc/elasticsearch/elasticsearch.yml
change localhost to our ip 


sudo vi /opt/fsf/fsf-client/conf/config.py
change ip: 172.16.10.100

sudo vi /opt/fsf/fsf-server/conf/config.py
change ip: 172.16.10.100
sudo systemctl retstart fsf


curl http://172.16.10.100:9200/_cat/indices
