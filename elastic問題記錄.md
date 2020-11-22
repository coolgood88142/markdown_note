## Elastic 文章

#### 1.安裝kibana Logs

windows要自行下載`filebeat-7.10.0-windows-x86_64.zip`後解壓縮

將`filebeat.yml`這幾段註解拿掉

```
setup.kibana:
	host: "localhost:5601"

output.elasticsearch:
  hosts: ["localhost:9200"]
  username: "elastic"
  password: "changeme"
```

開啟cmd，路徑到ELK的filebeat-7.10.0-windows-x86_64，執行下面的指令

讀取filebeat.yml

```
.\filebeat.exe -c filebeat.yml -e
```

啟動filebeat的system模組

```
.\filebeat.exe modules enable system
```

啟動filebeat

```
.\filebeat.exe setup -e
```

https://stackoverflow.com/questions/41751605/running-filebeat-in-windows

#### 2.安裝metricbeat

windows要自行下載`metricbeat-7.10.0-windows-x86_64.zip`後解壓縮

將`metricbeat.yml`這幾段註解拿掉

```
setup.kibana:
	host: "localhost:5601"

output.elasticsearch:
  hosts: ["localhost:9200"]
  username: "elastic"
  password: "changeme"
```

開啟cmd，路徑到ELK的metricbeat-7.10.0-windows-x86_64，執行下面的指令

讀取metricbeat.yml

```
.\metricbeat.exe -c metricbeat.yml -e
```

安裝system.logs

```
.\metricbeat.exe modules enable system
```

啟動metricbeat

```
.\metricbeat.exe setup -e
```



#### 3.安裝APM

windows要自行下載`apm-server-7.10.0-windows-x86_64.zip`後解壓縮

將`apm-server.yml`這幾段註解拿掉

```
output.elasticsearch:
  hosts: ["localhost:9200"]
  username: "elastic"
  password: "changeme"
```

開啟cmd，路徑到ELK的metricbeat-7.10.0-windows-x86_64，執行下面的指令

讀取apm-server.yml

```
.\apm-server.exe -c apm-server.yml -e
```

啟動apm-server

```
.\apm-server.exe setup -e
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

執行不要換行，這裡要注意範例中的反斜線不要用，會錯誤

```
java -javaagent:./elastic-apm-agent-1.19.0.jar 
     -Delastic.apm.service_name=my-application 
     -Delastic.apm.server_urls=http://localhost:8200
     -Delastic.apm.secret_token= 
     -Delastic.apm.application_packages=org.example 
     -jar ./target/opbeans-0.0.1-SNAPSHOT.jar
     
     java -javaagent:./elastic-apm-agent-1.18.0.RC1.jar -Delastic.apm.service_name=opbeans-java -Delastic.apm.server_urls=http://localhost:8200 -Delastic.apm.application_packages=co.elastic.apm.opbeans -jar ./target/opbeans-0.0.1-SNAPSHOT.jar


```

PowerShell.exe -ExecutionPolicy UnRestricted -File .\install-service-metricbeat.ps1.

#### 4.workplace search

- 本機的C:\Users\coolg\Desktop\ELK\kibana-7.9.0-windows-x86_64\config\kibana.yml，新增hosts之後在重新啟動kibana

  ```
  enterpriseSearch.host: 'http://localhost:3002'
  ```





#### 5.App search

- 與workplace search都屬於
- 



#### 安裝過程遇到的問題?

##### 1. 安裝System logs

- Elastic-7.10版本，System logs更換成Kibana Logs，安裝步驟沒變

- windows無法安裝`filebeat`

  要透過`filebeat`安裝`System logs`，自行下載`filebeat-7.10.0-windows-x86_64.zip`

- filebeat的filebeat.yml，要設定ElasticSearch的帳號密碼

  要先找出當初本機的帳號密碼

- 安裝時顯示錯誤訊息

  Exiting: Failed to create Beat meta file: open C:\Program Files\Filebeat\data\meta.json.new: Access is denied.

  https://discuss.elastic.co/t/filebeat-on-kubernetes-access-denied/152678/6

  將C:\Users\coolg\Desktop\ELK\filebeat-7.9.0-windows-x86_64\modules.d\system.yml檔名改成system.yml.disabled

- [Data Visualizer](http://localhost:5601/app/ml#/datavisualizer)無法update file

  上傳後

  

  

##### 2.安裝System metrics

- 要透過`Metricbeat`安裝`System metrics`

- Metricbeat的filebeat.yml，要設定ElasticSearch的帳號密碼

  要先找出當初本機的帳號密碼

  

##### 3.安裝APM

- 要透過`opbeans-java`安裝`APM Agents`

- APM的apm-server.yml，要設定ElasticSearch的帳號密碼

  要先找出當初本機的帳號密碼

- 執行javaagent有問題

  確認好Delastic.apm.secret_token的資料怎麼設定

- 安裝時顯示錯誤訊息

  1. ERROR co.elastic.apm.agent.report.IntakeV2ReportingEventHandler - Error trying to connect to APM Server. Some details about SSL configurations corresponding the current connection are logged at INFO level

     java JVM與APM Server版本沒對應上，導致安裝錯誤，要更換`elastic-apm-agent.jar`版本

  2. 安裝成功後ServiceMap沒在執行

     要更新7.8.0以上才可以

     參考資料：https://discuss.elastic.co/t/service-map-not-working/244000

  

  

  

  

  

  

  