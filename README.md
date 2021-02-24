# vue x drone x docker

此專案為 `vue cli` 建構的工程，使用 `drone` 來做自動化流程

## 操作

如果你要運行

- PULL DOCKER HUB IMAGE
  ```
  docker pull naikyding/drone-vue
  ```
- 運行專案
  這個 `container` 名稱為 `my-vue-app`，運行的 `image` 為 `naikyding/drone-ci`，將 `container` 內部的 `8081` 對接到本機的 `8080`。

  ```
  docker run -it -p 8080:8081 --name my-vue-app naikyding/drone-ci
  ```

## 流程

- 開發
- push feature/\* brance
- feature/\* -> develop (發 pr)
- (auto) Drone -> run unit test [Pedding]
- (auto) Drone -> unit test [Verified]
- 同意合拼
- develop -> master (發 pr)
- (auto) Drone -> docker [Pedding]
  - build
  - push docker hub

## drone pipeline 流程管道

於專案建立 `.drone.yml` 寫入方法

- `name: Drone ci` 這個自動化流程的名稱
- `steps` 步驟，一個流程中，可以有很多執行的項目，你可以把它當成一個**階段** 你希望它為你做什麼事，而階段就由觸發條件來定義與分類。
- `trigger`
  觸發 `.drone.yml` 的條件。(這個一定要寫上，不然 `steps` 會一定被任易條件觸發)

## docker

```yml
FROM node:lts-alpine

# install simple http server for serving static content
RUN npm install -g http-server

# make the 'app' folder the current working directory
WORKDIR /app

# copy both 'package.json' and 'package-lock.json' (if available)
COPY package*.json ./

# install project dependencies
RUN npm install

# copy project files and folders to the current working directory (i.e. 'app' folder)
COPY . .

# build app for production with minification
RUN npm run build

# container 內的埠號
EXPOSE 8081
CMD [ "http-server", "./dist" ]
```
