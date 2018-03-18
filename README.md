# Python3 With Selenium

オフィシャルのPython3コンテナを元に調整を行いました。

### 調整内容

* locale設定 (ja.utf-8)
* タイムゾーン （Asia/Tokyo）
* 必要なツールのインストール  
※ less vim
* selenium

### 使い方

下記のコマンドにてコンテナを起動します

```
$ docker pull reflet/docker-python3-selenium
$ docker run -d --name python3-selenium reflet/docker-python3-selenium
$ docker run exec -it python3-selenium bash
```

### docker-composeの記述例

```
version: '2'
services:
    selenium-hub:
        restart: always
        image: selenium/hub
        container_name: 'selenium-hub'
        ports:
            - '4444:4444'

    chrome:
        restart: always
        image: selenium/node-chrome-debug
        container_name: 'chrome'
        environment:
            - HUB_PORT_4444_TCP_ADDR=selenium-hub
            - HUB_PORT_4444_TCP_PORT=4444
        ports:
            - '5900:5900'
        volumes:
            - /dev/shm:/dev/shm
        depends_on:
            - selenium-hub

    python3-selenium:
        restart: always
        image: reflet/docker-python3-selenium
        container_name: 'python3-selenium'
        working_dir: '/root/'
        command: 'tail -f /dev/null'
        volumes:
            - ./opt/:/root/opt/
```

## Seleniumの動作確認について

docker-composeで起動します。

```
$ docker-compose up -d
```

python3-seleniumコンテナへ接続する。

```
$ docker exec -it python3-selenium bash
```

下記コマンドにて、簡易テストを実行してみる

```
# python
Python 3.6.2 (default, Aug 29 2017, 05:52:11) 
[GCC 4.9.2] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> from selenium import webdriver
>>> from selenium.webdriver.common.desired_capabilities import DesiredCapabilities
>>> driver = webdriver.Remote(
    command_executor='http://selenium-hub:4444/wd/hub',
    desired_capabilities=DesiredCapabilities.CHROME)
>>> driver.get("http://qiita.com/reflet")
>>> driver.close()
```

※ VNCでサーバへ接続していれば、画面にてchromeが起動し、qiitaの画面へ移動することが確認できます。

以上