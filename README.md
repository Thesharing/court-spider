# Judgement Document Spider

## Installation

### Git

Use `git clone git@github.com:Thesharing/court-spider.git --recursive ` to clone this repo into local.

### Python

*Linux and Windows:*

Requires [Python](https://www.python.org/downloads/) >= 3.5, and the newest version of [Anaconda 3](https://www.anaconda.com/download/) is better.

```bash
conda install certifi chardet idna numpy requests six urllib3 werkzeug Flask lxml python-dateutil
pip install opencv-python PyExecJS pymongo redis
```

If you are using pure python, install all the packages via pip rather than conda.

You can use [virtualenv](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001432712108300322c61f256c74803b43bfd65c6f8d0d0000)  or [conda create](https://conda.io/docs/user-guide/tasks/manage-python.html) command to create an isolated environment.

### Redis

*Windows:*

Use <u>Ubuntu on bash</u> on Windows if you are using Windows 10. 

Use VMWare or VirtualBox if you are using Windows 8 or 7. In VMWare you need to config port forwarding of NAT network in order to access Redis in Windows.

You just need to run Redis in bash, other components can still run in Windows.

*Linux and Windows:*

```bash
wget http://download.redis.io/releases/redis-4.0.11.tar.gz
tar xzf redis-4.0.11.tar.gz
rm redis-4.0.11.tar.gz
mv redis-4.0.11 redis
cd redis
make
```

[Reference to install Redis](https://redis.io/download)

### NodeJS

*Linux:*

```bash
curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
sudo apt-get install -y nodejs
nodejs --version
```

*Windows:*

Download lastest LST version executable installer to install.

[Reference to install NodeJS](https://nodejs.org/zh-cn/download/package-manager/#debian-and-ubuntu-based-linux-distributions)

### Folders

*Linux:*

```bash
cd spider
mkdir content data download log temp
cd ../proxy
touch key.txt
```

*Windows:*

Mannually create the folders and files mentioned above.

## Config

Refer to [Thesharing/proxy_pool](https://github.com/Thesharing/proxy_pool) to modify the config of Redis.

Customize the spider in `config.json`:

```json
{
  "start": {
    "date": "2017-05-11",
    "district": "北京市",
    "court": null
  },
  "search": {
    "keyword": "*",
    "type": null,
    "reason": {
      "value": "知识产权与竞争纠纷",
      "level": 2
    },
    "court": {
      "value": null,
      "level": 0,
      "indicator": false
    },
    "district": null
  },
  "condition": {
    "法院层级": null,
    "案件类型": "民事案件",
    "审判程序": null,
    "文书类型": null
  },
  "config": {
    "max_retry": 10,
    "proxy": true,
    "timeout": 60
  }
}
```

At first run, copy `config.example.json` as `config.json` and modify the configs.

If you want to resume from a breakpoint, modify `start` part like this:

```json
{
  "start": {
    "date": "2017-05-11",
    "district": "北京市",
    "court": null
  },
  "...": "..."
}
```

## Execution

**First**, run Redis.

Use Ubuntu on bash on Windows if you are using Windows 10. 

(In the path of Redis)

```bash
src/redis-server
```

**Then**, run the proxy.

(In `court-spider/proxy`)

```bash
python main.py
```

Access [127.0.0.1:5010/get_status](http://127.0.0.1:5010/get_status) to check the status of proxy pool.

`raw_proxy` means unvalidated proxy, `useful_proxy` means validated proxy. 

Change the website used to validate the proxies in `Util/utilFunction.py`.

Make sure the number of `useful_proxy` > 0, so that the spider can run with available proxies.

**Finally**, run the spider.

(In `court-spider/spider/Run`)

```ba
python main.py -s date
```

##  Update

Run the commands below in the root folder of this project to receive the newest updates.

```bash
git pull
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

