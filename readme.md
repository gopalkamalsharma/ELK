# ELK Stack

## Setup

### Prerequsites

- Launch 2 EC2 machine with ubuntu with t2.medium type
- Name 1st as ELK and 2nd as Server
- On ELK machine install
  - Java
  ```
 sudo apt install openjdk-17-jre-headless
  ```
  - Elastic Search
  ```
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg
sudo apt-get install apt-transport-https
echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/9.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-9.x.list
sudo apt-get update && sudo apt-get install elasticsearch
  ```

### Configuring ELK Stack
Open this file and modify the below line
- sudo nano /etc/elasticsearch/elasticsearch.yml

```
network.host: 0.0.0.0
cluster.name: my-cluster
node.name: node-1
discovery.type: single-node
```

### Start and Enable the elasticsearch

```
sudo systemctl start elasticsearch
sudo systemctl enable elasticsearch
sudo systemctl status elasticsearch
```

## Installing Logstash

```
sudo apt install logstash -y
```

### Configuring Logstash

```
sudo nano /etc/logstash/conf.d/logstash.conf
```

Add these lines:
```
input {
  beats {
    port => 5044
  }
}
filter {
    grok {
      match => {"message" => "%{TIMESTAMP_ISO8601:log_timestamp} %{LOGLEVEL:log_level}
      %{GREEDYDATA:log_message}"}
output {
  elasticsearch{
    hosts => ["http://localhost:9200"]
    index => "logs-%{+YYYY.MM.dd}"
  }
    stdout {codec -> rubydebug}
}
```

