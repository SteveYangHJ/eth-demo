# Geth搭建私有链

Geth版本：1.8.2

##  多节点搭建 
前置：
	1. genesis.json
	2. 目录:
	    Geth目录：/opt/geth-1.8.2
	    区块目录：/opt/geth-chain/chain
### 方式1
    注：目前有问题，节点未加入同一个网络
	1. 多节点搭建：
	   采用开源shell脚本：https://github.com/ethersphere/eth-utils，脚本与最新Geth版本不兼容，需修改。
	2. 命令参考：
        -- start the cluster & the signle node
	    GETH=./geth bash gethcluster.sh ~/geth-yzt/chain 5565 3 05 172.16.0.13 -mine
	    GETH=./geth bash gethup.sh ~/geth-yzt/chain1 04 09 --mine console
	3. IPC endpoint: url=/root/geth-yzt/chain/5565/data/00/geth.ipc
	    可通过命令连接Console：./geth attach /root/geth-yzt/chain/5565/data/00/geth.ipc


### 方式2 (bootnode)
	1. 命令参考：
	节点1，    
    初始化创世区块： ./geth --datadir=/opt/geth-yzt/data/00 init genesis.json 
    启动节点：nohup ./geth --datadir /opt/geth-yzt/data/00 --networkid 314590 --port 61910 --rpc --rpcport 8200 >/opt/geth-yzt/log/00.log 2>&1 &

    节点2
    初始化创世区块： ./geth --datadir=/opt/geth-yzt/data/01 init genesis.json
    启动节点：nohup ./geth --datadir /opt/geth-yzt/data/01 --networkid 314590 --port 61911 --rpcport 8201 --bootnodes "enode://2b5c9480740b370d376e95c9e8f14bf7eed37da6bac9759edabb4bb8755f3d94b9f95d8b84b46a55ae3fc92425c9513b2c85307cc442fb958d0e996dc986b809@10.59.98.122:61910" >/opt/geth-yzt/log/01.log 2>&1 &
    
    节点3
    初始化创世区块： ./geth --datadir=/opt/geth-yzt/data/02 init genesis.json
    启动节点：nohup ./geth --datadir /opt/geth-yzt/data/02 --networkid 314590 --port 61912 --rpcport 8202 --bootnodes "enode://2b5c9480740b370d376e95c9e8f14bf7eed37da6bac9759edabb4bb8755f3d94b9f95d8b84b46a55ae3fc92425c9513b2c85307cc442fb958d0e996dc986b809@10.59.98.122:61910" >/opt/geth-yzt/log/02.log 2>&1 &

### Attach to the console
Command: `./geth attach /opt/geth-yzt/data/00/geth.ipc`
