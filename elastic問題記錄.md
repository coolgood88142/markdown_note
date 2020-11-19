## Elastic 文章

#### 1.安裝filebeat

windows要自行下載`filebeat-7.8.0-windows-x86_64.zip`後解壓縮

將`filebeat.yml`這幾段註解拿掉

```
setup.kibana:
	host: "localhost:5601"

output.elasticsearch:
  hosts: ["localhost:9200"]
  username: "elastic"
  password: "changeme"
```

開啟cmd，路徑到ELK的filebeat-7.8.0-windows-x86_64，執行下面的指令

讀取filebeat.yml

```
filebeat -c filebeat.yml -e

```

安裝system.logs

```
filebeat modules enable system
```

啟動filebeat

```
filebeat setup -e
```



#### 2.安裝metricbeat

windows要自行下載`metricbeat-7.8.0-windows-x86_64.zip`後解壓縮

將`metricbeat.yml`這幾段註解拿掉

```
setup.kibana:
	host: "localhost:5601"

output.elasticsearch:
  hosts: ["localhost:9200"]
  username: "elastic"
  password: "changeme"
```

開啟cmd，路徑到ELK的metricbeat-7.8.0-windows-x86_64，執行下面的指令

讀取metricbeat.yml

```
metricbeat -c metricbeat.yml -e

```

安裝system.logs

```
metricbeat modules enable system
```

啟動metricbeat

```
metricbeat setup -e
```



#### 3.安裝APM

windows要自行下載`apm-server-7.8.0-windows-x86_64.zip`後解壓縮

將`apm-server.yml`這幾段註解拿掉

```
output.elasticsearch:
  hosts: ["localhost:9200"]
  username: "elastic"
  password: "changeme"
```

開啟cmd，路徑到ELK的metricbeat-7.8.0-windows-x86_64，執行下面的指令

讀取apm-server.yml

```
apm-server -c apm-server.yml -e

```

啟動apm-server

```
apm-server setup -e
```

下載opbeans-java

```
git clone https://github.com/elastic/opbeans-java.git
```

開啟cmd，路徑到ELK的C:\Users\coolg\Desktop\ELK\opbeans-java\opbeans，執行 **mvn** 指令產生 **./target/opbeans-0.0.1-SNAPSHOT.jar** 檔案

```
mvn package
```

在C:\Users\coolg\Desktop\ELK\opbeans-java\opbeans，執行以下這些指令

執行不要換行

```
java -javaagent:./elastic-apm-agent-1.19.0.jar \
     -Delastic.apm.service_name=my-application \
     -Delastic.apm.server_urls=http://localhost:8200 \
     -Delastic.apm.secret_token= \
     -Delastic.apm.application_packages=org.example \
     -jar ./target/opbeans-0.0.1-SNAPSHOT.jar
```



#### 4.workplace search

本機的kibana 並沒有workplace search，只能在Elastic Cloud手動建立



#### 安裝過程遇到的問題?

##### 1. 安裝System logs

- windows無法安裝`filebeat`

  要透過`filebeat`安裝`System logs`，自行下載`filebeat-7.8.0-windows-x86_64.zip`

- filebeat的filebeat.yml，要設定ElasticSearch的帳號密碼

  要先找出當初本機的帳號密碼

- 安裝時顯示錯誤訊息

  Exiting: Failed to create Beat meta file: open C:\Program Files\Filebeat\data\meta.json.new: Access is denied.

  https://discuss.elastic.co/t/filebeat-on-kubernetes-access-denied/152678/6

- 用本機安裝的話，安裝步驟要不一樣

  1. 先執行`filebeat -c filebeat.yml -e`
  2. 跑完在執行`filebeat modules enable system`
  3. 裝完在執行filebeat -e

  

##### 2.安裝System metrics

- 要透過`Metricbeat`安裝`System metrics`

- Metricbeat的filebeat.yml，要設定ElasticSearch的帳號密碼

  要先找出當初本機的帳號密碼

- 安裝時顯示錯誤訊息

  Exiting: Failed to create Beat meta file: open C:\Program Files\Metricbeat\data\meta.json.new: Access is denied.

##### 3.安裝APM

- 要透過`opbeans-java`安裝`APM Agents`

- APM的apm-server.yml，要設定ElasticSearch的帳號密碼

  要先找出當初本機的帳號密碼

- 執行javaagent有問題

  確認好Delastic.apm.secret_token的資料怎麼設定

  

  

  
  
  