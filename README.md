# 需要安装的开发环境
* docker 1.12.6
* docker-compose 1.11.2
* golang 1.8
* nodejs 6.9.5
* git

# fabric v alpha 1.0 编译

以下是fabric alpha 1.0 编译过程，因为每个人环境不一样，执行过程可能会有错误，如果有错误请大家到[区块链兄弟](http://www.blockchainbrother.com/) 上贴出问题，我们会抽出时间统一回答。

### 安装其他必须的组件

* Ubuntu 安装

```
apt-get install libltdl-dev
```

* CentOS 安装

```
yum install   -y gcc libtool libltdl-dev libtool-ltdl-devel openssl
```

* Mac 安装
```
brew install libtool openssl
```


1. 下载fabric 源码

```
   git clone https://github.com/hyperledger/fabric.git
```
   

2. 进入fabric 目录

```
   git reset --hard f3c61e6cc3b04915081b15bbed000b377b53c4c1
```

3. 执行 make peer orderer 命令

# fabric v alpha 1.0 部署

如果上一步骤没有编译成功，接下来的操作也可以完成，我把编译成功的order，peer 二进制文件 copy到以下目录中：
![Alt text](./images/pic1.png)
> bin 目录下的peer,orderer 可以在Ubuntu，centOS 环境下执行，如果是windows,mac 环境需按照上一章节的步骤自行编译。

在部署节点之前首先做以下操作
1. 拉取镜像
执行fabric_images下pull.sh脚本

### 部署order

执行fabric_orderer_release下order.sh，启动order节点
```shell

root@gcc2ge:/home/fabric_alpha1_bootscript/fabric_orderer_release# ./orderer.sh 

```


### 部署peer

使用方法
```
root@gcc2ge:/home/fabric_alpha1_bootscript/fabric_peer_release# ./peer.sh -?
Invalid option: -?
Usage: 
 ./peer.sh [opt] [value] 
    -p: peer id, default=peer0
    -l: listen address, default=0.0.0.0:7051
    -g: gossip externalendpoint, default=0.0.0.0:7051
    -o: order ip address, default=0.0.0.0:7050
    -m: msp path, default=/home/fabric_alpha1_bootscript/fabric_peer_release/msp/crypto-config/peerOrganizations/peerOrg1/peers/peerOrg1Peer1
    -O: Organization, default=PeerOrg1
 
 example: 
 ./peer.sh -p peer0  -l 172.16.10.81:7051 -g 172.16.10.81:7051 -o 172.16.10.82:7050 -m /home/fabric_alpha1_bootscript/fabric_peer_release/msp/crypto-config/peerOrganizations/peerOrg1/peers/peerOrg1Peer1 -O PeerOrg1 
 ```
#### example 启动一个peer节点 
> IP 一定要用本地IP地址，不要用0.0.0.0,127.0.0.1

```
/peer.sh -p peer0  -l 172.16.10.81:7051 -g 172.16.10.81:7051 -o 172.16.10.81:7050 -m /home/fabric_alpha1_bootscript/fabric_peer_release/msp/crypto-config/peerOrganizations/peerOrg1/peers/peerOrg1Peer1 -O PeerOrg1 
```

### 创建通道

```
root@gcc2ge:/home/fabric_alpha1_bootscript/fabric_peer_release/peer# ./create.sh 
```

### 加入通道

```
root@gcc2ge:/home/fabric_alpha1_bootscript/fabric_peer_release/peer# ./join.sh 
```

### 执行环境变量

```
root@gcc2ge:/home/fabric_alpha1_bootscript/fabric_peer_release/peer# source ./env.sh
```

### 安装chaincode

```
peer chaincode install -o 172.16.10.81:7050 -n marbles1 -v 1 -p github.com/hyperledger/fabric/examples/chaincode/go/marbles02
```

### instantiate

```
peer chaincode instantiate -o 172.16.10.81:7050 -C testChannel1 -n marbles1 -v 1 -p github.com/hyperledger/fabric/examples/chaincode/go/marbles02 -c '{"Args":[""]}'
```

### invoke

```
peer chaincode invoke -o 172.16.10.81:7050 -C testChannel1 -n marbles1 -c '{"Args":["getHistoryForMarble","marble1"]}
```

### fabric-ca 的编译和安装使用过程

1. git clone https://github.com/hyperledger/fabric-ca  

2. 进入目录cmd/fabric-ca-server  执行  go install  生成fabric-ca 服务器端程序

3. 进入目录cmd/fabric-ca-client  执行  go install 生成fabric-ca 客户端程序

4. 启动服务器 fabric-ca-server init --boot admin:adminpw

5. 注册用户fabric-ca-client register --id.name peer2 --id.type peer --id.affiliation org1.department1 --id.secret peer2wd -u http://172.16.10.81:19900

6. 导入用户
fabric-ca-client enroll -u http://peer2:peer2wd@172.16.10.81:19900 -M /usr/fabric/peers/msp111/sampleconfig


目前181上面已经安装有一个ca服务器供大家学习参考


如果您在配置过程中遇到问题欢迎加入  [区块链兄弟](http://www.blockchainbrother.com/) 参与讨论
