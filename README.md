# Geth搭建私有链

Geth版本：1.8.2
##  安装Geth

 1. 环境准备：
 ubuntu 14.0.6，
 Windows 7
 2. 源码编译安装
安装GO：https://github.com/ethereum/go-ethereum/wiki/Installing-Go#ubuntu-1404
Ubuntu安装Geth：https://github.com/ethereum/go-ethereum/wiki/Installation-Instructions-for-Ubuntu

 3. release版本安装
 下载i编译的二进制版本：https://gethstore.blob.core.windows.net/builds/geth-alltools-linux-amd64-1.8.2-b8b9f7f4.tar.gz

> 需要注意的是，Linux环境和Windows环境的差别，以及Linux环境下安装geth命令的不同方式。源码编译安装，geth已加入环境变量，可直接在任意目录执行命令，如：geth --datadir=/opt/geth-chain/data/00 account new；而解压缩已编译geth，则需要以./geth方式。

##  多节点搭建 
前置：
	1. 创建一个新账户：
	`geth --datadir=/opt/geth-chain/data/00 account new`
	2. 初始化创世区块：
	  创建genesis.json
	`{
　　"config":{
　　　　"chainId":123456,
　　　　"homesteadBlock":0,
　　　　"eip155Block":0,
　　　　"eip158Block":0
　　},
　　"alloc":{},
　　"coinbase":"0x0000000000000000000000000000000000000000",
　　"difficulty":"0x020000",
　　"extraData":"",
　　"gasLimit":"0x2fefd8",
　　"nonce":"0x0000000000000042",
　　"mixhash":"0x0000000000000000000000000000000000000000000000000000000000000000",
　　"parentHash":"0x0000000000000000000000000000000000000000000000000000000000000000",
　　"timestamp":"0x00"
}`
	3. 目录:
	    `Geth目录：/opt/geth-1.8.2
        区块目录：/opt/geth-chain/data`

### 方式1,
    注：目前有问题，节点未加入同一个网络
	1. 多节点搭建：
	   采用开源shell脚本：https://github.com/ethersphere/eth-utils，脚本与最新Geth版本不兼容，需修改。
	2. 命令参考：
        -- start the cluster & the signle node
	    GETH=./geth bash gethcluster.sh ~/geth-chain/data 5565 3 05 172.16.0.13 -mine
	    GETH=./geth bash gethup.sh ~/geth-chain/data 04 09 --mine console
	3. IPC endpoint: url=/root/geth-chain/chain/5565/data/00/geth.ipc
	    可通过命令连接Console：./geth attach /root/geth-chain/chain/5565/data/00/geth.ipc


### 方式2, bootnode方式
	1. 命令参考：
	节点1，    
    初始化创世区块：./geth --datadir=/opt/geth-chain/data/00 init genesis.json 
    启动节点：nohup ./geth --datadir /opt/geth-chain/data/00 --networkid 314590 --port 61910 --rpc --rpcport 8200 >/opt/geth-chain/log/00.log 2>&1 &
    
    获取Node1的enode信息，然后修改一下命令

    节点2
    初始化创世区块： ./geth --datadir=/opt/geth-chain/data/01 init genesis.json
    启动节点：nohup ./geth --datadir /opt/geth-chain/data/01 --networkid 314590 --port 61911 --rpcport 8201 --bootnodes "enode://2b5c9480740b370d376e95c9e8f14bf7eed37da6bac9759edabb4bb8755f3d94b9f95d8b84b46a55ae3fc92425c9513b2c85307cc442fb958d0e996dc986b809@10.59.98.122:61910" >/opt/geth-chain/log/01.log 2>&1 &
    
    节点3
    初始化创世区块： ./geth --datadir=/opt/geth-chain/data/02 init genesis.json
    启动节点：nohup ./geth --datadir /opt/geth-chain/data/02 --networkid 314590 --port 61912 --rpcport 8202 --bootnodes "enode://2b5c9480740b370d376e95c9e8f14bf7eed37da6bac9759edabb4bb8755f3d94b9f95d8b84b46a55ae3fc92425c9513b2c85307cc442fb958d0e996dc986b809@10.59.98.122:61910" >/opt/geth-chain/log/02.log 2>&1 &


### 命令行交互
Attach to the console command: `./geth attach /opt/geth-chain/data/00/geth.ipc`

Some Command:
Reference: 
 1. [Management-APIs][1]
 2. [JSON-RPC API][2]

List all Accounts: `eth.accounts`
Create new Account: `personal.newAcccount("pwd")`
Unlock one Account: `personal.unlockAccount(eth.accounts[0],"pwd")`
View the Balance: `eth.getBalance(eth.accounts[0])`

View Default Miner Account: `eth.coinbase`
Change the BaseMiner Account: `miner.setEtherbase(eth.accounts[1])`
Start/Stop to Mine: `miner.start(1)/miner.stop()`

admin
节点加入网络:admin.addPeer("enode://03f411e46212e00fb3824db08e1df03715bab0908a1f129367bba8564e728dfcd4581c90b0acc5b9ee7e8f3c47ada524a18b76bd117ef30aaa4dcab2faea80c2@[IP]:30303")


List the BlockNum: `eth.blockNumber`
View the Block info: `eth.getBlock(1)`

Send a new Transcation: `eth.sendTransaction({from: eth.accounts[0], to:eth.accounts[1], value: web3.toWei(1, "ether")})`
List the Pending Transcation：`eth.pendingTransactions`
View the Transcation: `eth.getTransaction(tranId)`

Contract:
initialize deployed contract : `eth.contract(abi).at(contractAddress)`
Deploy contract: `eth.contract(abi).new(input_params)`;

需要注意：
执行合约方法时，需要指定eth.defaultAccount: `web3.eth.defaultAccount=eth.accounts[0]`;否则会报错：Error: invalid address




### Ether币的基本单位
Ether币最小的单位是Wei，也是上面命令行显示数字的默认单位, 每1000个进一个单位，依次是
kwei (1000 Wei)
mwei (1000 KWei)
gwei (1000 mwei)
szabo (1000 gwei)
finney (1000 szabo)
ether (1000 finney)
即，1 ether = 1000000000000000000 Wei

ether 和 Wei转换：`web3.fromWei(10000000000000000)`, `web3.toWei(0.01)`

### 



  [1]: https://github.com/ethereum/go-ethereum/wiki/Management-APIs
  [2]: https://github.com/ethereum/wiki/wiki/JSON-RPC
