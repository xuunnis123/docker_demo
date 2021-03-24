# Docker

###### tags: docker


### Docker
- Containers as a Service (CaaS)
- 應用程式中心的虛擬化
```
AppA      |   AppB
Bins/Libs | Bins/Libs
     Docker Engine
        Host OS
        Server
```
## Docker三個基本概念
如果映像檔是一個做蛋糕的模具，容器則是用該模具烤出來的蛋糕，而倉庫是用來集中放置模具們的收納櫃。
```Image(映像檔)```/```Container(容器)```/```Repository(倉庫)```
#### Image:
- Docker 映像檔是一個模板，用來重複產生容器實體。例如：一個映像檔裡可以包含一個完整的 MySQL 服務、一個 Golang 的編譯環境、或是一個 Ubuntu 作業系統。
- 透過 Docker 映像檔，我們可以快速的產生可以執行應用程式的容器。而 Docker 映像檔可以透過撰寫由命令行構成的 Dockerfile 輕鬆建立，或甚至可以從公開的地方下載已經做好的映像檔來使用。
- Docker映像檔就是唯獨的模板
- 映像檔可以用來建立Docker容器

透過 Docker 映像檔，我們可以快速的產生可以執行應用程式的容器。而 Docker 映像檔可以透過撰寫由命令行構成的 Dockerfile 輕鬆建立，或甚至可以從公開的地方下載已經做好的映像檔來使用。

如果我今天想要一個 node.js 的執行環境跑我寫好的程式，我可以直接到上 DockerHub 找到相對應的 node.js 映像檔 ，而不需要自己想辦法打包一個執行環境。
#### Container:
- 容器是從映像檔建立的執行實例
- Docker利用容器來執行應用
- 可以被啟動/開始/停止/刪除
- 每個容器都是相互隔離的，保證安全的平台
容器是用映像檔建立出來的執行實例。它可以被啟動、開始、停止、刪除。每個容器都是相互隔離、保證安全的平台。

> Docker 映像檔是唯讀（read-only）的，而容器在啟動的時候會建立一層可以被修改的可寫層作為最上層，讓容器的功能可以再擴充。

#### Respository:
- 放所有映像檔檔案的地方
- 每個倉庫又包含多個映像檔
- 每個映像檔有不同標籤(tag)
- 倉庫分為公開倉庫跟私有倉庫

最大的公開倉庫註冊伺服器是上面提到過的``` Docker Hub```，存放了數量龐大的映像檔供使用者下載，我們可以輕鬆在上面找到各式各樣現成實用的映像檔。
而 Docker 倉庫註冊伺服器的概念就跟 Github 類似，你可以在上面建立多個倉庫，然後透過 push、pull 的方式上傳、存取。

建立第一個Docker Image
Tips: 
- Web App 程式打包成Docker Image並執行成Container

[參考來源]
https://cwhu.medium.com/docker-tutorial-101-c3808b899ac6

git clone https://github.com/HcwXd/docker-tutorial.git
之後再檔案路徑 cd 到C:\Users\xuunn\OneDrive\文件\GitHub\docker-tutorial\docker-demo-app中有五個檔案：
> .
> ├── .dockerignore
> ├── Dockerfile
> ├── docker.html
> ├── index.js
> └── package.json

其中　docker.html、index.js、package.json
三個檔案為我們要打包的Web App

> 大致概念為:建立一個Server監聽在3000的port，收到request進來後會渲染docker.html這個檔案，顯示出html裡面的東西

##### 撰寫Dockerfile
Docker透過撰寫命令行告訴Docker應該要如何打包我們的程式
``` CMD=
FROM node:10.15.3-alpine
WORKDIR /app
ADD . /app
RUN npm install
EXPOSE 3000
CMD node index.js
```

```FROM node:10.15.3-alpine```
這行會載入 Node.js 需要的執行環境，每個不同的程式需要的環境可能都不同，這裏下載的是 node:10.15.3-alpine，詳細的其他版本可以在 Dockerhub 上看到

```WORKDIR /app```
在這個 Docker 的環境之中建立一個工作目錄 /app

```ADD . /app```
把跟 Dockerfile 同個資料夾的程式加到剛建立的工作目錄 /app 中

```RUN npm install```
運行 npm install，讓 npm 透過讀取 package.json 下載相依的 package

```EXPOSE 3000```
指定 container 對外開放的 port

```CMD node index.js```
我們透過 node index.js 來執行我們的 Server

在寫完 Dockerfile 後，如果你有在本機端執行 `npm install` 去產生 `node_modules` 的話，記得加一個 `.dockerignore` 檔案並指定去忽略 `node_modules`。

打包程式:
我們可以在資料夾內透過指令 `docker build`
`docker build . -t docker-demo-app`
去建立 Docker Image 並為這個 Image 加上 tag `docker-demo-app`。

然後我們可以再透過指令
`docker images`
列出我們全部的 Docker Image 如下
```
REPOSITORY                      TAG       IMAGE ID       CREATED         SIZE
docker101tutorial               latest    da83d7142e95   7 minutes ago   27.9MB
xuunnis1011/docker101tutorial   latest    da83d7142e95   7 minutes ago   27.9MB
hello-world                     latest    d1165f221234   2 weeks ago     13.3kB
alpine/git                      latest    a939554ad0d0   4 weeks ago     25.1MB
```
現在我們就打包好了我們的第一個 Docker Image 囉！

##### 實際執行成 Container
透過上面的 docker images 指令，我們可以找到我們建立 Image 的 ID，在這邊是  `da83d7142e95`

`docker run -p 3000:3000 -it da83d7142e95`

透過 `docker run`，我們實際把 Image 執行成 Container 了！這時我們看到 terminal 顯示 `listening on port 3000` 後，用瀏覽器打開 [localhost:3000](localhost:3000)，就可以迎接一隻 Docker 鯨魚。

##### 關閉Container
接下來要如何關掉 Docker Container 呢？如果發現送 `ctrl + c `的 signal 進去 Docker Container 沒有反應的話，我們可以開啟另外一個 terminal，然後透過指令 `docker ps`找到運行中的 Container ID，然後在輸入` docker stop <ContainerID>`，就可以優雅地關掉我們剛執行的 Docker Container。

再次運用蛋糕的比喻，假設我們今天要做一個 Docker 吉祥物形狀的蛋糕如上。首先我們要先用專門做蛋糕底座的模具烤出蛋糕底座，然後在這個出爐的基底上，再用鯨魚模具烤出鯨魚形狀的蛋糕。現在我們有了一個新鮮的蛋糕有堅固的底座與一隻鯨魚躺在上面，最後我們在這個蛋糕上面放上一個貨櫃模具，烤出 Docker 蛋糕。

還記得我們在上面提到，映像檔是唯讀的，所以我們每次要在映像檔上面再疊一層時，都需要先將他建立成容器實體（烤成蛋糕本人）。然後運用容器啟動時會在最上面建立可寫層的特性，我們就可以在容器裡在透過另外一個映像檔建立容器實體，最後把這個容器疊上容器的碗糕轉換成映像檔（Dockerize），我們就完成了映像檔堆疊。
用 Docker 堆疊出一個網頁環境

現在我們要做出一個 Docker 映像檔，讓我們執行網頁與其後端伺服器，假設我們需要 Alpine OS、Apache Server、MySQL Database、我們要如何把他們疊起來呢？
首先我們要先釐清一個概念，為何 Docker 裡面還需要一個作業系統？

事實上在容器的世界裡，這個作業系統我們稱為 Base OS。我們可以把它想像成一個超級精簡版的作業系統（像我們這邊使用的 Apline 就只要 5 MB），因為容器事實上是使用 Host OS 上的 Kernel，所以這個 Base OS 就只要裝最必要的一些執行檔，如：安裝網路套件的工具等。

疊疊樂的第一步就是從 DockerHub 上下載 Alpine 映像檔，用 Docker 執行映像檔產生容器後，在產生好的容器內在安裝 Apache，等待安裝完成後把整個容器打包成另一個新的映像檔。
而第二層繼續反覆這樣的程序，從 Alpine-Apache 建立的映像檔透過 Docker 產生新的容器，在容器裡安裝下一層 MySQL，然後把整個容器打包，我們就得到一個含有 Alpine、Apache、MySQL 的映像檔了！

---

延伸閱讀:
https://philipzheng.gitbook.io/docker_practice/
https://blog.techbridge.cc/2018/09/07/docker-compose-tutorial-in

執行一個ubuntu系統
---
使用 ```docker images``` 指令:可顯示本機已有的images
```
REPOSITORY                      TAG       IMAGE ID       CREATED        SIZE
xuunnis1011/docker101tutorial   latest    da83d7142e95   22 hours ago   27.9MB
hello-world                     latest    d1165f221234   2 weeks ago    13.3kB
```

其中```tag```用來標記同一個repository的不同image。
(image可能具有相同的image id 。表示這些是相同的image)

### 取得Image
前面docker在執行container前會去檢查是否本機有對應的image。如果不存在，則會去registry下載。
可使用```docker pull``` 指令，從registry取得所需的image。

```docker pull ubuntu:latest```


表示會從registry下載一個ubuntu的image，tag為 latest
由於預設的registry是Docker Hub，因此該指令等同於
```docker pull registry.hub.docker.com/ubuntu:latest```
表示會從registry.hub.docker.com下載一個hello-world的image，tag為 latest


* `Error response from daemon: manifest for ubuntu:lastest not found: manifest unknown: manifest unknown`
這裡錯誤的意思是docker需要我們指定下載映象的版本號。

解決方式：我們可以登入：[docker hub](https://hub.docker.com/u/library)，搜尋自己想要下載的映象名，搜尋過程可以確認現在有哪些tags可以使用

docker build
---

docker build的運作流程如下：
1. 首先會將Dockerfile的內容上傳，之後所有操作會依據Dockerfile來執行。
2. 接著每一步都會建立一個新的容器，執行command 與commit的動作。
3. 最後回傳一個image id，並將中間步驟所產生的container都會被刪除。


https://medium.com/@VisonLi/docker-%E5%85%A5%E9%96%80-%E7%AD%86%E8%A8%98-part-2-91e4dfa2b365



VS code上操作docker
https://docs.microsoft.com/zh-tw/learn/modules/use-docker-container-dev-env-vs-code/2-use-as-development-environment