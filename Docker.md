### 介紹Docker

### 什麼是Docker?

Docker 是一種軟體平台，可讓您快速地建立、測試和部署應用程式。讓開發人員將應用程式包裝到容器，這是一種標準化的可執行元件，將應用程式原始碼與在任何環境中執行該程式碼所需的作業系統 (OS) 程式庫及相依關係結合在一起。

### 優點

- 輕量型：占用的資訊不大，能夠快速地啟動
- 提高效率：利用容器，您可以在相同硬體上，使用VM來執行多個應用程式。
- 提高生產力：

使用 Docker 可快速交付程式碼、標準化應用程式操作、無縫移動程式碼，以及透過提高資源使用率節省成本。您可以使用 Docker 獲得能夠隨處可靠執行的單一物件。Docker 簡單易懂的語法還能為您提供完整的控制權。廣泛採用代表有穩固的工具和立即可用應用程式生態系統隨時可供 Docker 使用。

### Docker 三元素

- 映像檔(Image)：映像檔是一個模板，用來重複產生容器實體。例如：一個映像檔裡可以包含一個完整的 MySQL 服務、一個 Golang 的編譯環境、或是一個 Ubuntu 作業系統。

  透過 Docker 映像檔，我們可以快速的產生可以執行應用程式的容器。

- 容器(Container)：容器是用映像檔建立出來的執行實例。可以把容器看做是一個執行的應用程式加上執行它的VM。

- 倉庫(Repository）：存放映像檔的空間，倉庫註冊伺服器（Registry）上則存放著多個倉庫。

  可以用Docker Hub存放了數量龐大的映像檔供使用者下載，我們可以輕鬆在上面找到各式各樣現成實用的映像檔。

### 安裝

1. 從[官網](https://docs.docker.com/desktop/mac/install/)上安裝

2. 安裝完之後打包程式

   ```bash
   git clone https://github.com/HcwXd/docker-tutorial.git
   ```

   我們要建立一個 Server 使用 3000 port，在網頁上顯示小鯨魚圖示。

3. 撰寫檔案

   ```dockerfile
   //Dockerfile 
   FROM node:10.15.3-alpine
   WORKDIR /app
   ADD . /app
   RUN npm install
   EXPOSE 3000
   CMD node index.js
   ```

4. 執行指令

   開`cmd`進入檔案目錄，執行安裝，在安裝之後用`.dockerignore`，新增`node_modules`，避免要放`Docker Hub`會把`node_modules`上傳上去

   ```
   npm install
   ```

5. 編譯程式

   將`docker-demo-app`整個資料夾進去打包

   ```bash
   docker build . -t docker-demo-app
   ```

   輸入docker images指令，顯示所有的圖片

   ```
   REPOSITORY        TAG     IMAGE ID       CREATED           SIZE
   docker-demo-app   latest  733776b1db0a   8 minutes ago     74MB
   ```

6. 執行成 Container

   ```
   docker run -p 3000:3000 -it 733776b1db0a
   ```

   這時打開瀏覽器，輸入localhost:3000，要關閉時用ctrl+c

   ![docker1](<https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/docker1.png>)

### 參考資料

https://cwhu.medium.com/docker-tutorial-101-c3808b899ac6

https://www.ibm.com/tw-zh/cloud/learn/docker

https://zh.wikipedia.org/wiki/Docker
