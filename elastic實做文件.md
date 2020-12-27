## Elastic 文章

以下介紹，在kibana實用的功能 

### 1.建立Logs

在首頁的Observability選擇Add sample data

![elastic_demo1](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo1.png>)

在選擇Sample web logs的Add data，點選Logs

![elastic_demo2](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo2.png>)

建立成功後，進入Logs頁面，第一個是左上的**搜索條（Search bar）**可以篩選Logs上的關鍵字，第二個是中上的 **Highlights**，可以突顯、強調你想要看的文字內容。

![elastic_demo3](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo3.png>)

#### 安裝System Logs

要透過filebeat來安裝，先下載`filebeat-7.10.0-windows-x86_64.zip`後解壓縮，在將`filebeat.yml`這幾段註解拿掉

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

啟動filebeat的system logs

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

#### 安裝System metricbeat

要透過metricbeat安裝System metricbeat，先下載`metricbeat-7.10.0-windows-x86_64.zip`後解壓縮，在將`metricbeat.yml`這幾段註解拿掉

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

啟動metricbeat的system metricbeat

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

這裡要注意，指令不要換行，不然會拋錯

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

#### 6.Kibana Simple Logs

在首頁的Observability選擇Add sample data，將三種範例點選Add data

![elastic_demo17](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo17.png>)

左邊的面板 Management 下面，選擇Stack Management，接著選擇 Index Patterns。

![elastic_demo18](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo18.png>)

在從Create index pattern，接著輸入 kibana_sample_data_logs\*，然後按下 Next step。

![elastic_demo19](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo19.png>)

![elastic_demo20](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo20.png>)

在 Time field的欄位，選擇 timestamp，按下 Create index pattern，就會看到創建出來索引的所有 fields 啦！

![elastic_demo21](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo21.png>)

![elastic_demo22](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo22.png>)

在 bytes 欄位右邊的鉛筆符號按下去後，可以把原本預設是 Number的格式改成 Bytes後按下 Save。

![elastic_demo23](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo23.png>)

![elastic_demo24](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo24.png>)

回到 Discover 的面板，把 index pattern 改成我們剛才創建的 kibana_sample_data_logs\，查詢logs資料

![elastic_demo25](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo25.png>)

![elastic_demo26](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo26.png>)

用 Add filter 的方式，在Logs過濾Windows資料

- Field：machine.os.keyword
- Operator：in one of
- Values：win xp、win 8
- Custom label：WinOS

![elastic_demo27](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo27.png>)

再新增fiter CN的資料

![elastic_demo28](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo28.png>)



在建立Data Table查看Client IP的資料，回到首頁點選Visualize，選擇 Create visualization，接著選 Data Table

![elastic_demo29](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo29.png>)

![elastic_demo30](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo30.png>)

選擇剛剛建立的kibana_sample_data_logs*

![elastic_demo31](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo31.png>)

點選Buckets按下 Add後，選擇 Split rows

![elastic_demo32](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo32.png>)

接著在設定選項

- Aggregation：Terms
- Field：clientip
- Size：10
- Custom label：Client IP

![elastic_demo33](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo33.png>)

顯示Logs中有出現哪些IP資料

![elastic_demo34](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo34.png>)

#### 7.建立客製化圖表-圓餅圖

在首頁點選Dashboard，使用我們前面建好的log Web Traffic

![elastic_demo35](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo35.png>)

來源國家選擇 CN，OS 選擇 win 8、win xp，可以看到單一訪客剩下 224 個，這裡分析的是單一個使用者資料，不是我們想要的

![elastic_demo36](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo36.png>)

接下來到回到首頁點選Visualize，選擇 Create visualization，接著選 Pie

![elastic_demo37](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo37.png>)

從右邊的 Buckets，選擇 Add，Split slices，在設定下面圖片的選項，點選Update

![elastic_demo38](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo38.png>)

在設定Options的選項，要在按一次Update

![elastic_demo39](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo39.png>)

大功告成，內圈是各個國家的比例，外圈則是各個國家使用系統的比例

![elastic_demo40](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo40.png>)

左上角有個save可以做儲存，打上名稱後按save

![elastic_demo41](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/elastic_demo41.png>)

#### 安裝過程遇到的問題?

##### 1. 安裝System logs

- Elastic-7.10版本，System logs更換成Kibana Logs，安裝步驟沒變

- 安裝時顯示錯誤訊息

  Exiting: Failed to create Beat meta file: open C:\Program Files\Filebeat\data\meta.json.new: Access is denied.

  https://discuss.elastic.co/t/filebeat-on-kubernetes-access-denied/152678/6

  將C:\Users\coolg\Desktop\ELK\filebeat-7.9.0-windows-x86_64\modules.d\system.yml檔名改成system.yml.disabled


##### 2.安裝APM

- 安裝時顯示錯誤訊息

  1. ERROR co.elastic.apm.agent.report.IntakeV2ReportingEventHandler - Error trying to connect to APM Server. Some details about SSL configurations corresponding the current connection are logged at INFO level

     java JVM與APM Server版本沒對應上，導致安裝錯誤，要更換`elastic-apm-agent.jar`版本

  2. 安裝成功後ServiceMap沒在執行

     要更新7.8.0以上才可以

     參考資料：https://discuss.elastic.co/t/service-map-not-working/244000



參考資料：https://stackoverflow.com/questions/41751605/running-filebeat-in-windows









