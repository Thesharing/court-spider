

<div align="center">
    <img src='./img/logo.png' height="192px"/>
    <h1>
        Judgement Document Spider
    </h1>
</div>

## Table of Contents

* [Installation](#--------installation----)
  * [Git](#Git)
  * [Python](#Python)
  * [Redis](#Redis)
  * [MongoDB](#MongoDB)
  * [NodeJS](#NodeJS)
  * [New Folders](#Create-New-Folders)
* [Config](#--------config----)
* [Execution](#--------execution----)
  * [Instructions](#Instructions)
  * [Functions](#Functions) 
  * [Argument Lists](#Argument-Lists)
* [Update](#--------update----)
* [Commit](#--------commit----)
* [TODO List](#--------todo-list----)
* [Reference](#--------reference----)

<div align="center">
    <img src='./img/installation.png' height="128px"/>
    <h2>
        Installation
    </h2>
</div>

### Git

Use `git clone git@github.com:Thesharing/court-spider.git --recursive ` to clone this repo into local.

### Python

*For Linux or Windows:*

Requires [Python](https://www.python.org/downloads/) >= 3.5, and the newest version of [Anaconda 3](https://www.anaconda.com/download/) is better.

```bash
conda install certifi chardet idna numpy requests six urllib3 werkzeug Flask lxml python-dateutil beautifulsoup4 lxml
pip install opencv-python PyExecJS pymongo redis
```

\* If you are using pure python, install all the packages via pip rather than conda.

\* You can use [virtualenv](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001432712108300322c61f256c74803b43bfd65c6f8d0d0000)  or [conda create](https://conda.io/docs/user-guide/tasks/manage-python.html) command to create an isolated environment.

**NOTE**

*For Windows:*

Edit [path of site-packages](https://stackoverflow.com/questions/122327/how-do-i-find-the-location-of-my-python-site-packages-directory)/execjs/\_external\_runtime.py and replace the function `_exec_with_pipe` like this:

Original `_exec_with_pipe` :

```python
def _exec_with_pipe(self, source):
    cmd = self._runtime._binary()
    p = None
    try:
        p = Popen(cmd, stdin=PIPE, stdout=PIPE, stderr=PIPE, cwd=self._cwd, universal_newlines=True)
        input = self._compile(source)
        if six.PY2:
            input = input.encode(sys.getfilesystemencoding())
        stdoutdata, stderrdata = p.communicate(input=input)
        ret = p.wait()
    finally:
        del p

    self._fail_on_non_zero_status(ret, stdoutdata, stderrdata)
    return stdoutdata
```

Edited `_exec_with_pipe` :

```python
def _exec_with_pipe(self, source):
    cmd = self._runtime._binary()

    p = None
    try:
        p = Popen(cmd, stdin=PIPE, stdout=PIPE, stderr=PIPE, cwd=self._cwd, universal_newlines=False)
        input = self._compile(source).encode(sys.getfilesystemencoding())
        stdoutdata, stderrdata = p.communicate(input=input)
        ret = p.wait()
    finally:
        del p

    self._fail_on_non_zero_status(ret, stdoutdata, stderrdata)
    return stdoutdata.decode(sys.getdefaultencoding())
```

And then the execjs library will work out fine.

\* Or install PyExecJS with `pip install https://github.com/Thesharing/PyExecJS.git` instead of original PyExecJS.

### Redis

*For Windows:*

Use <u>Ubuntu on bash on Windows</u> if you are using Windows 10. 

Use VMWare or VirtualBox if you are using Windows 8 or 7. In VMWare you need to config port forwarding of NAT network in order to access Redis in Windows.

\* You just need to run Redis in bash, other components can still run in Windows.

\* Also you can install pre-compiled version for Windows referring to this page: [Redis Install | Runoob](http://www.runoob.com/redis/redis-install.html)

*For Linux, or Bash on Ubuntu on Windows:*

```Bash
wget http://download.redis.io/releases/redis-4.0.11.tar.gz
tar xzf redis-4.0.11.tar.gz
rm redis-4.0.11.tar.gz
mv redis-4.0.11 redis
cd redis
make
```

[Reference to install Redis](https://redis.io/download)

### MongoDB

*For Linux:*

Refer to [the official installation document](https://docs.mongodb.com/manual/administration/install-on-linux/).

*For Windows*:

[Download the executable installer](https://www.mongodb.com/download-center/community) and run it, follow the installer guide. After finishing the installation MongoDB will start automatically. 

Also refer to [the official installation document](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/).

\* You can run the [MongDB Compass](https://docs.mongodb.com/compass/master/install/) to see if MongoDB is ready.

### NodeJS

*For Linux:*

```Bash
curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
sudo apt-get install -y nodejs
nodejs --version
```

*For Windows:*

Download lastest LST version executable installer to install.

[Reference to install NodeJS](https://nodejs.org/zh-cn/download/package-manager/#debian-and-ubuntu-based-linux-distributions)

### Create New Folders

*For Linux or Windows:*

```bash
cd spider
mkdir content data download log temp
```

<div align="center">
    <img src='./img/config.png' height="128px"/>
    <h2>
        Config
    </h2>
</div>


Refer to [Thesharing/proxy_pool](https://github.com/Thesharing/proxy_pool) to modify the config of Redis in proxy.

At first run, copy `config.example.json` as `config.json` and modify the configs.

Customize the spider in `config.json`:

```
{
  "start": { 						// 设定Spider的起始日期、地区和法院
    "date": "2017-05-11",
    "district": "北京市",
    "court": null
  },
  "search": {						// 设定检索条件，这些会生成文字版的检索条件
    "keyword": "*", 				// 关键词
    "type": null, 					// 案件类型
    "reason": {						// 案由
      "value": "知识产权与竞争纠纷",	  // 案由名称
      "level": 2 // 案由等级，例如二级案由
    },
    "court": {						// 法院
      "value": null,				// 法院名称
      "level": 0,					// 法院层级，例如中级人民法院
      "indicator": false			// 是检索当前层级还是其下一层级的法院
    },
    "district": null 				// 地区
  },
  "condition": {
    "法院层级": null,  				 // 检索条件，这些会直接加到检索条件里
    "案件类型": "民事案件",
    "审判程序": null,
    "文书类型": null
  },
  "config": { 						// Spider程序设定
    "max_retry": 10, 				// 抓取失败时的最大重试次数
    "proxy": true, 					// 是否通过代理抓取
    "timeout": 60 					// 抓取的超时时间
  },
  "database": { 					// 数据库设定
    "redis": {						// Redis
      "host": "localhost", 			// 地址
      "port": 6379 					// 端口号
    },
    "mongodb": {					// MongoDB
      "host": "localhost", 			// 地址
      "port": 27017, 				// 端口号
      "database": "spider" 			// 集合名称
    }
  },
  "log": { 							// 日志设置
    "level": "INFO" 				// 日志等级
      								//有DEBUG, INFO, WARNING, ERROR, CRITICAL几个等级
  },
  "multiprocess": { 				// 多进程模式设置
    "total": 8, 					// 总进程数
    "spider": 0, 					// Spider数量
    "downloader": 8, 				// Downloader数量
    "notifier": 0 					// Notifier数量
  },
  "notifier": {						// Notifier设置
    "type": "wechat",				// 类型
    "period": 10,					// 监视周期
    "wechat": {						// 微信模式设置
      "receiver": "filehelper", 	// 接收人的微信名
      "cmd": false					// 在命令行中显示二维码还是保存成图片
    },
    "email": { 						// 邮件模式设置
      "sender": "aaa@bbb.ccc",		// 发送邮箱地址
      "password": "aaabbbccc",		// 发送邮箱的密码
      "server_addr": "smtp.bbb.ccc", // 发送邮箱的STMP地址
      "receiver": "aaa@bbb.ccc",	// 接收消息的邮箱地址
      "ssl": false					// 是否通过SSL加密链接
    }
  }
}
```

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

<div align="center">
    <img src='./img/execution.png' height="128px"/>
    <h2>
        Execution
    </h2>
</div>

### Instructions

**First**, run Redis:

*\*Use Ubuntu on bash on Windows if you are using Windows 10.\** 

(In the folder path of Redis)

```bash
src/redis-server
```

**Second**, run MongoDB:

*For Windows:*

If you install MongoDB as service, you can start it in `services.msc`. Usually it starts automatically.

*For Linux:*

 `sudo service mongod start`

**Third**, run Proxy.

(In the folder path `court-spider/proxy/Run`)

```bash
python main.py
```

Access [127.0.0.1:5010/get_status](http://127.0.0.1:5010/get_status) to check the status of proxy pool.

`raw_proxy` means the count of unvalidated proxy, `useful_proxy` means the count of validated proxy. 

You can change the website used to validate the proxies in `Util/utilFunction.py`.

Make sure the number of `useful_proxy` > 0, so that the spider can run with available proxies.

**Finally**, run Spider with the functions you need as indicated below.

(In folder path `court-spider/spider`)

### Functions

#### Spider

To run **spider**, run the command below:

```bash
python main.py spider --date
```

#### Downloader

To run **downloader**, run the command below:

```bash
python main.py downloader --download
```

Before you run the downloader, you may need to extract all the DocID from local files stored in `./temp`  to Redis. If you want to download docs from DocID in `./data`, you need to move it from `./data` to `./temp` **manually**, and move back to `./data` after reading. Then run the command below:

```bash
python main.py downloader --read
```

#### Specify config file

If you are running two o more task at the same time, you can specify **different configs** like this:

```bash
python main.py --config config.json
```

#### Multi-Process Mode

To enable **multi-process** mode, specify the multi-process config in config.json like this:

```json
{
    "...": "...",
  "multiprocess": {
    "total": 8,
    "spider": 0,
    "downloader": 8,
    "notifier": 0
  },
  "...": "..."
}
```

`total` is the total number of processes  to run spiders and downloaders, which should be equal to the number of logic cores of the CPU on your device (you can see the number in Task Manager in Windows, or run `grep -c ^processor /proc/cpuinfo` in Linux).

`spider` , `downloader`  and `notifier` is the number of spiders and downloaders (at present multi-process spider is not supported).

Then run without none of `spiser` ,  `downloader`  and `notifier` like this:

```bash
python main.py
```

### Argument Lists

Optional arguments:

| Argument name          | Short name       | Explanation                              |
| ---------------------- | ---------------- | ---------------------------------------- |
| spider --district      | s --district     | Start a spider to crawl data by district |
| downloader --clean     | d --clean        | Delete all the data in Redis             |
| downloader --read      | d --read         | Read content from doc-id files           |
| downloader --download  | d --download     | Start a downloader                       |
| notifier               | n                | Start a notifier                         |
| --config [config.json] | -c [config.json] | Specify the filename of config           |
| --help                 | -h               | Show the help message                    |

`spider` , `downloader`and `notifier` should not be used at the same time.

Enable multi-process mode if none of `spider`, `downloader` and `notifier` is not used.

<div align="center">
    <img src='./img/update.png' height="128px"/>
    <h2>
        Update
    </h2>
</div>

Run the commands below in the root folder of this project to fetch the newest updates.

```bash
git pull
git submodule update --recursive --remote
```

To sync commits with upstream repos,  refer to this doc: [Syncing a fork | Github](https://help.github.com/articles/syncing-a-fork/).

<div align="center">
    <img src='./img/commit.png' height="128px"/>
    <h2>
        Commit
    </h2>
</div>

If you want to commit changes to this repo, please first `push` the changes in the submodule, then go back to the root folder of this project and run the commands below.

```bash
git submodule update --remote
git add . 
git commit -m '<Commit Message>'
git push
```

<div align="center">
    <img src='./img/todo-list.png' height="128px"/>
    <h2>
        TODO List
    </h2>
</div>

- [x] Downloader

- [x] Multi-process support for Document Downloader

- [x] Notifier

- [ ] Extractor [ONGOING]

    - [ ] 争议焦点划分

    - [ ] 审判程序之间的关联

    - [ ] 诉讼参与人信息拆分

    - [ ] 整段文段的划分

- [ ] Crawl by date

- [ ] Multi-process support for Task Distributor and Content List Downloader

<div align="center">
    <img src='./img/reference.png' height="128px"/>
    <h2>
        Reference
    </h2>
</div>

[sixs/wenshu_spider](https://github.com/sixs/wenshu_spider)

[jhao104/proxy_pool](https://github.com/jhao104/proxy_pool)

[doloopwhile/PyExecJS](https://github.com/doloopwhile/PyExecJS)



-----

<div align="center">
 <pre>
_________                   _____     ________       ______________            
__  ____/_________  __________  /_    __  ___/__________(_)_____  /____________
_  /    _  __ \  / / /_  ___/  __/    _____ \___  __ \_  /_  __  /_  _ \_  ___/
/ /___  / /_/ / /_/ /_  /   / /_      ____/ /__  /_/ /  / / /_/ / /  __/  /    
\____/  \____/\__,_/ /_/    \__/      /____/ _  .___//_/  \__,_/  \___//_/     
                                             /_/                               
 </pre>
</div>