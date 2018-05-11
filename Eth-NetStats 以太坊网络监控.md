# Eth-NetStats 以太坊网络监控

标签： 以太坊 ethereum eth-netstats

---

## Network Status Monitoring 网络状态监控
 Ethereum (centralised) network status monitor(有时被称为eth-netstats)，是一个基于Web的应用程序，用于通过一组节点监控testnet/miannet的健康状况。
 以太坊公有链监控系统：https://ethstats.net/
 
网络监控由两部分组成：
 1. [eth-net-intelligence-api][1]：与ethereum一起运行的backend后端服务，其跟踪网络状态，通过JSON-RPC获取信息，并通过WebSocket连接到eth-netstats以提供信息。
 2. [eth-netstats][2]：用于跟踪ethereum网络状态的可视化界面，它使用WebSocket从运行节点接受状态信息，并通过Angular接口输出。它是eth-net-intelligence-api的前端实现。其展示的数据主要包括：最新区块号、叔节点、最后区块产生时间、平均块生成时间、平均网络哈希率、难度值、活动节点数、GAS价格、节点信息列表等

## 监控关系
在eth-netstats配置过程中，需要注意的是eth/geth、eth-net-intelligence-api和eth-netstats之间的接口关系，主要接口关系如下图：
![监控流程图][3]
eth-net-intelligence-api通过rpc调用获取eth/geth节点上的区块链监控数据，eth-netstats与eth-net-intelligence-api之间使用websocket进行通信，将区块链数据通过angular接口可视化成图表信息展示出来。

## eth-net-intelligence-api
### 依赖组件
依赖组件或前置条件：

 - eth, geth or pyethapp 
 - node & npm

### 安装
源码安装：

    git clone https://github.com/cubedro/eth-net-intelligence-api
    cd eth-net-intelligence-api
    npm install
    sudo npm install -g pm2

### 运行
修改app.json，配置自己的以太坊网络地址：

 - LISTENING_PORT：监听端口号，默认为30303
 - INSTANCE_NAME：以太坊节点名称 
 - CONTACT_DETAILS：你的联系方式
 - RPC_HOST：以太坊节点RPC主机地址
 - RPC_PORT：以太坊节点RPC端口，默认8545
 - WS_SERVER：eth-netstats监控的websocket主机地址
 - WS_SECRET：eth-netstats监控的websocket口令
 
配置完成后，运行命令：`pm2 start app.json`，启动eth-net-intelligence-api服务。

一些可用命令:
`pm2 list` to display the process status，显示进程状态;
`pm2 logs` to display logs，显示日志;
`pm2 gracefulReload node-app` for a soft reload，软重装;
`pm2 stop node-app` to stop the app，停止应用程序;
`pm2 kill` to kill the daemon，杀死守护进程
`pm2 status`: 显示状态

  
## eth-netstats
### 依赖组件
依赖组件或前置条件：

 - eth, geth or pyethapp 
 - node & npm
 - pm2

### 安装
源码安装：

    git clone https://github.com/cubedro/eth-netstats
    cd eth-netstats
    npm install
    sudo npm install -g grunt-cli

Netstats功能可生成两个版本：完整版本和轻量级版本。为了构建静态资源，需要运行grunt命令，这将生成包含js、css文件、字体和图片的dist或dist-lite目录。
生成完整版本，运行`grunt`；生成lite版本，运行`grunt lite`；或者两个版本一起生成，运行`grunt all`。

### 运行
启动：`PORT=3000 WS_SECRET={WS_SECRET} npm start`，其中WS_SECRET为eth-net-intelligence-api的配置文件app.json中配置的口令。
 

## 可能的安装问题
### Node & NPM 版本问题
1，实践中发现node版本为0.10.25，npm版本为1.3.10；安装eth-netstats后运行`PORT=3000 WS_SECRET={WS_SECRET} npm start`失败，报错：

    SyntaxError: Use of const in strict mode

可能是因为版本问题，故通过以下命令更新node&npm：

    npm cache clean -f     // 清除node.js的cache
    sudo npm install -g n  // 安装n工具，专门用来管理node.js版本的
    sudo n stable          // 安装stable版本

2，NPM SSL问题
通过npm install 命令安装第三方库时，可能会出现以下错误：
`npm http GET https://registry.npmjs.org/n
npm ERR! Error: CERT_UNTRUSTED`
证书问题，可临时设置：`npm config set strict-ssl false`解决该问题。可参考[ssl-error-cert-untrusted-while-using-npm-command][4]。


  [1]: https://github.com/cubedro/eth-net-intelligence-api
  [2]: https://github.com/cubedro/eth-netstats
  [3]: http://mmbiz.qpic.cn/mmbiz/0UraoBrX9mlFlCsiaWs30kxdV5mYeicATaiaHWKcJvib9u62zHCAaKoHZRoRxkLkthL9ApCNrqicbhMgNp0XlcYhbKg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1
  [4]: https://stackoverflow.com/questions/21855035/ssl-error-cert-untrusted-while-using-npm-command
