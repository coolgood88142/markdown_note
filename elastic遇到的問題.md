### Elastic 文章在安裝過程遇到的問題?



#### 1. 安裝System logs

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

  

#### 2.安裝**`System metrics`**

- 要透過`Metricbeat`安裝`System metrics`

- Metricbeat的filebeat.yml，要設定ElasticSearch的帳號密碼

  要先找出當初本機的帳號密碼

- 安裝時顯示錯誤訊息

  Exiting: Failed to create Beat meta file: open C:\Program Files\Metricbeat\data\meta.json.new: Access is denied.

