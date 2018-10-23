# Judgement Document Spider

## Installation

### Git

Use `git clone git@github.com:Thesharing/court-spider.git --recursive ` to clone this repo into local.

### Python

Requires python >= 3.5, better use anaconda.

```bash
conda install certifi chardet idna numpy requests six urllib3 werkzeug Flask lxml python-dateutil
pip install opencv-python PyExecJS pymongo redis
```

### Redis

[Reference](https://redis.io/download)

```bash
wget http://download.redis.io/releases/redis-4.0.11.tar.gz
tar xzf redis-4.0.11.tar.gz
rm redis-4.0.11.tar.gz
mv redis-4.0.11 redis
cd redis
make
```

### Nodejs

[Reference](https://nodejs.org/zh-cn/download/package-manager/#debian-and-ubuntu-based-linux-distributions)

```bash
curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
sudo apt-get install -y nodejs
nodejs --version
```

### Folders

```bash
cd spider
mkdir content data download log temp
cd ../proxy
touch key.txt
```

## Config

Refer to [Thesharing/proxy_pool](https://github.com/Thesharing/proxy_pool) to modify the config of Redis.

Customize the spider in config.py and main.py.

To resume an interrupted process, copy start_example.json as start.json and modify the content like:

```json
{"district": "北京市", "date": "1990-01-01"}
```

## Execution

First, run Redis.

Use Ubuntu on bash on Windows if you are using Windows.

(In the path of Redis)

```bash
src/redis-server
```

Then run proxy.

(In `court-spider/proxy`)

```bash
python main.py
```

Access 127.0.0.1/get_status to check the status of proxy pool.

`raw_proxy` means unvalidated proxy, `useful_proxy` means validated proxy. Change the website used to validate the proxies in `Util/utilFunction.py`.

Make sure the number of `useful_proxy` > 0, so that the spider can run with available proxies.

Finally, run the spider.

(In `court-spider/spider`)

```ba
python main.py
```

##  Update

Run the command below in the root folder of this project to receive the newest updates.

```bash
git submodule update --recursive --remote
```

## Commit

If you want to commit changes to this repo, please first `push` the changes in the submodule, then go back to the root folder of this project and run the commands below.

```bash
git submodule update --remote
git add . 
git commit -m '<Commit Message>'
git push
```

## Reference

[sixs/wenshu_spider](https://github.com/sixs/wenshu_spider)

[jhao104/proxy_pool](https://github.com/jhao104/proxy_pool)

