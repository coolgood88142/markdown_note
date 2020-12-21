## Elastic 文章

以下介紹，在kibana實用的功能 

### 1.建立Logs

在首頁的Observability選擇Add sample data

![elastic_demo1](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo1.png>)

在選擇Sample web logs的Add data，點選Logs

![elastic_demo2](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo2.png>)

建立成功後，進入Logs頁面，第一個是左上的**搜索條（Search bar）**可以篩選Logs上的關鍵字，第二個是中上的 **Highlights**，可以突顯、強調你想要看的文字內容。

![elastic_demo3](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo3.png>)

#### 安裝filebeat

下載`filebeat-7.10.0-windows-x86_64.zip`後解壓縮，在將`filebeat.yml`這幾段註解拿掉

```
setup.kibana:
	host: "localhost:5601"

output.elasticsearch:
  hosts: ["localhost:9200"]
  username: "elastic"
  password: "changeme"
```

開啟cmd，路徑到ELK的filebeat-7.10.0-windows-x86_64，執行下面的指令

用PowerShell.exe執行install-service-filebeat.ps1

```
PowerShell.exe -ExecutionPolicy UnRestricted -File .\install-service-filebeat.ps1.
```

執行filebeat.yml

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

以上步驟做完後，回到kibana，在儀表板(Dashboards)搜尋System，可以選擇各式各樣的圖表

![elastic_demo4](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo4.png>)

![elastic_demo5](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo5.png>)



### 2.建立Metricbeat

#### 安裝metricbeat

下載`metricbeat-7.10.0-windows-x86_64.zip`後解壓縮，在將`metricbeat.yml`這幾段註解拿掉

```
setup.kibana:
  host: "localhost:5601"

output.elasticsearch:
  hosts: ["localhost:9200"]
  username: "elastic"
  password: "changeme"
```

開啟cmd，路徑到ELK的metricbeat-7.10.0-windows-x86_64，執行下面的指令

用PowerShell.exe執行install-service-metricbeat.ps1

```
PowerShell.exe -ExecutionPolicy UnRestricted -File .\install-service-metricbeat.ps1.
```

執行metricbeat.yml

```
.\metricbeat.exe -c metricbeat.yml -e
```

啟動metricbeat的system模組

```
.\metricbeat.exe modules enable system
```

啟動metricbeat

```
.\metricbeat.exe setup -e
```

以上步驟做完後，回到kibana，在儀表板(Dashboards)搜尋Metricbeat System

![elastic_demo6](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo6.png>)

### 3.APM

#### 安裝apm-server

自行下載`apm-server-7.10.0-windows-x86_64.zip`後解壓縮，在將`apm-server.yml`這幾段註解拿掉

```
output.elasticsearch:
  hosts: ["localhost:9200"]
  username: "elastic"
  password: "changeme"
```

開啟cmd，路徑到ELK的metricbeat-7.10.0-windows-x86_64，執行下面的指令

用PowerShell.exe執行install-service-apm-server.ps1

```
PowerShell.exe -ExecutionPolicy UnRestricted -File .\install-service-apm-server.ps1
```

執行apm-server.yml

```
.\apm-server.exe -c apm-server.yml -e
```

啟動apm-server

```
.\apm-server.exe setup -e
```

#### 下載opbeans-java

![elastic_demo7](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo7.png>)

```
git clone https://github.com/elastic/opbeans-java.git
```

開啟cmd，路徑到ELK的C:\Users\coolg\Desktop\ELK\opbeans-java\opbeans，執行 mvn 指令產生 /target/opbeans-0.0.1-SNAPSHOT.jar檔案

```
mvn package
```

以上步驟做完後，回到kibana，選擇APM Agents 的頁面，選到 Java的部分，點選 Maven Central後，選擇 jar檔案下載，下載好後把檔案移至剛才 opbeans 的路徑下。

![elastic_demo8](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo8.png>)

![elastic_demo9](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo9.png>)

在C:\Users\coolg\Desktop\ELK\opbeans-java\opbeans，執行以下這些指令

這裡要注意，不要換行，會錯誤

```
java -javaagent:./elastic-apm-agent-1.18.0.RC1.jar -Delastic.apm.service_name=opbeans-java -Delastic.apm.server_urls=http://localhost:8200 -Delastic.apm.application_packages=co.elastic.apm.opbeans -jar ./target/opbeans-0.0.1-SNAPSHOT.jar
```

執行之後，在瀏覽器輸入http://localhost:8080/

![elastic_demo10](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo10.png>)

我們進入錯誤的頁面，：http://localhost:8080/is-it-coffee-time，讓opbeans產生logs

![elastic_demo11](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo11.png>)

回到 APM頁面在重新整理，就會顯示opbeans-java的資料

![elastic_demo12](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo12.png>)

在點選Service Map，進入畫面在點選opbeans-java圖示，再點選Service Details

![elastic_demo13](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo13.png>)

點選之後，就會顯示opbeans-java各種圖表

![elastic_demo14](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo14.png>)



### 4.Filebeat NGINX

在filebeat-7.9.0-windows-x86_64\modules.d\nginx.yml，修改內容

```
module: nginx
  access:
    var.paths: ["C:\Users\coolg\Desktop\ELK\log\nginx\access.log*"]
  error:
    var.paths: ["C:\Users\coolg\Desktop\ELK\log\nginx\error.log*"]
```

在重新啟動Filebeat 

```
.\filebeat.exe setup -e
```

回到儀表板(Dashboards)，搜尋nginx，點選[File Nginx] Overview ECS，再點選Nginx access and error logs，就看到Filebeat 蒐集 NGINX logs

![elastic_demo15](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo15.png>)



#### 5.Metricbeat  NGINX 

在metricbeat-7.9.0-windows-x86_64\modules.d\metricbeat.yml修改內容

```
metricbeat.config.modules:
  path: ${path.config}/modules.d/*.yml
  reload.enabled: false
setup.template.settings:
  index.number_of_shards: 1
  index.codec: best_compression
setup.kibana:
  host: "server1:5601"
output.elasticsearch:
  hosts: ["server1:9200"]
processors:
  - add_host_metadata: ~
  - add_cloud_metadata: ~
```

再從在metricbeat-7.9.0-windows-x86_64\modules.d\system.yml修改內容

```
- module: system
  period: 10s
  metricsets:
    - cpu
    - load
    - memory
    - network
    - process
    - process_summary
    - socket_summary
  process.include_top_n:
    by_cpu: 5      # include top 5 processes by CPU
    by_memory: 5   # include top 5 processes by memory
- module: system
  period: 1m
  metricsets:
    - filesystem
    - fsstat
  processors:
  - drop_event.when.regexp:
      system.filesystem.mount_point: '^/(sys|cgroup|proc|dev|etc|host|lib)($|/)'
- module: system
  period: 15m
  metricsets:
    - uptime
```

在metricbeat-7.9.0-windows-x86_64\modules.d\nginx.yml修改內容

```
- module: nginx
  period: 10s
  hosts: ["http://172.18.0.1"]
  server_status_path: "server-status"
```

再重新啟動metricbeat

```
.\metricbeat.exe setup -e
```

回到儀表板(Dashboards)，搜尋nginx，點選[Metricbeat Nginx] Overview ECS

![elastic_demo16](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo16.png>)

#### 6.APM agents 

在apm-server-7.9.0-windows-x86_64\modules.d\ apm-server.yml修改內容

```
apm-server:
    # 定義 host 和 port 讓 APM server 偵聽
    host: "0.0.0.0:8200"
  
rum:
    # 啟動 real user monitoring (RUM)
    enabled: true
```

啟動apm-server

```
.\apm-server.exe setup -e
```

執行下面這段

```
java -javaagent:/home/elastic/petclinic/elastic-apm-agent-1.12.0.jar  \
    -Delastic.apm.service_name=petclinic-spring  \
    -Delastic.apm.server_urls=http://localhost:8200  \
    -jar /home/elastic/petclinic/spring-petclinic-1.5.16.jar
```



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

##### 4.安裝workplace search和App search

- workplace search和App search都是來自於Enterprise Search，但是Enterprise Search並沒有提供window package 

  

參考資料：https://stackoverflow.com/questions/41751605/running-filebeat-in-windows









