### <font style="color:rgb(31, 35, 40);">搭建单节点私有链</font>
使用go-ethereum搭建一个私有链，版本如下

```plain
-- 下载源码
git clone https://github.com/ethereum/go-ethereum.git

-- 选择v1.11.6分支
-- 编译
make geth
-- 构建
./build/bin/geth

geth version
sudo ln -s ./build/bin/geth /usr/local/bin/geth
vim ~/.zshrc
export PATH=$PATH:~/go/src/go-ethereum/build/bin
source ~/.zshrc
-- 安装完成，查看geth版本
geth version

Geth
Version: 1.11.6-stable
Git Commit: ea9e62ca3db5c33aa7438ebf39c189afd53c6bf8
Architecture: arm64
Go Version: go1.20.14
Operating System: darwin
GOPATH=/Users/rick/go
GOROOT=/usr/local/go

```

**<font style="color:rgb(31, 35, 40);">先创建测试用的 data 目录</font>**

```bash
mkdir test_ethereum
mkdir -p test_ethereum/data
vi test_ethereum/genesis.json
```

```json
{
  "config": {
    "chainId": 1,
    "homesteadBlock": 0,
    "eip150Block": 0,
    "eip155Block": 0,
    "eip158Block": 0,
    "byzantiumBlock": 0,
    "constantinopleBlock": 0,
    "petersburgBlock": 0,
    "istanbulBlock": 0,
    "berlinBlock": 0,
    "londonBlock": 0
  },
  "alloc": {
    "0x0000000000000000000000000000000000000001": {
      "balance": "111111111"
    },
    "0x0000000000000000000000000000000000000002": {
      "balance": "222222222"
    }
  },
  "coinbase": "0x0000000000000000000000000000000000000000",
  "difficulty": "0x20000",
  "extraData": "",
  "GasLimit": "0x2fefd8",
  "nonce": "0x0000000000000042",
  "mixhash": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "timestamp": "0x00"
}
```

<font style="color:rgb(31, 35, 40);">chainId 字段用于标识整个区块链，可以设置为任何正整数。通过将 nonce 修改为随机值，可以有效地防止未知节点连接到你的网络。alloc 字段则用于提前设置一些具有余额的账户</font>

**<font style="color:rgb(31, 35, 40);">初始化创世区块</font>**

```bash
geth -datadir test_ethereum/data init test_ethereum/genesis.json

INFO [07-24|21:22:11.389] Maximum peer count                       ETH=50 total=50
INFO [07-24|21:22:11.392] Set global gas cap                       cap=50,000,000
INFO [07-24|21:22:11.392] Initializing the KZG library             backend=gokzg
INFO [07-24|21:22:11.394] Using pebble as the backing database
INFO [07-24|21:22:11.394] Allocated cache and file handles         database=/Users/rick/go/myPrivateChain/data/geth/chaindata cache=16.00MiB handles=16
INFO [07-24|21:22:11.443] Opened ancient database                  database=/Users/rick/go/myPrivateChain/data/geth/chaindata/ancient/chain readonly=false
INFO [07-24|21:22:11.443] State scheme set to already existing     scheme=path
INFO [07-24|21:22:11.444] Opened ancient database                  database=/Users/rick/go/myPrivateChain/data/geth/chaindata/ancient/state readonly=false
INFO [07-24|21:22:11.444] Initialized path database                cache=16.00MiB buffer=64.00MiB history=90000
INFO [07-24|21:22:11.444] Successfully wrote genesis state         database=chaindata hash=0e5896..9d3a20
```

#### <font style="color:rgb(31, 35, 40);">创建账户</font>
```bash
geth --datadir test_ethereum/data account new

INFO [07-25|10:56:15.255] Maximum peer count                       ETH=50 LES=0 total=50
Your new account is locked with a password. Please give a password. Do not forget this password.
Password:
Repeat password:

Your new key was generated

Public address of the key:   0xfF96a2a7E05369f30bc971462DCd3B6a574cA13A
Path of the secret key file: test_ethereum/data/keystore/UTC--2025-07-25T02-56-24.415359000Z--ff96a2a7e05369f30bc971462dcd3b6a574ca13a

- You can share your public address with anyone. Others need it to interact with you.
- You must NEVER share the secret key with anyone! The key controls access to your funds!
- You must BACKUP your key file! Without the key, it's impossible to access account funds!
- You must REMEMBER your password! Without the password, it's impossible to decrypt the key!
```

<font style="color:rgb(31, 35, 40);">此时路径 </font>`<font style="color:rgb(31, 35, 40);background-color:rgba(129, 139, 152, 0.12);">test_ethereum/data/keystore/</font>`<font style="color:rgb(31, 35, 40);"> 下会多出一个文件对应这个新账户，内容是 JSON 格式，里面存储的是经过密码加密后的私钥信息。</font>

#### <font style="color:rgb(31, 35, 40);">运行节点，开始挖矿</font>
```bash
geth -datadir test_ethereum/data \
-networkid 1 \
-port "30303" \
-http \
-http.api "admin,debug,eth,miner,net,personal,txpool,web3" \
-http.port "8999" \
-http.corsdomain "*" -nodiscover \
-mine \
-miner.threads=1 \
-miner.etherbase 0x267A3C3a7D61F9e7D880E008446d435E9472ECD8



INFO [07-28|23:00:19.636] Maximum peer count                       ETH=50 LES=0 total=50
INFO [07-28|23:00:19.640] Set global gas cap                       cap=50,000,000
INFO [07-28|23:00:19.640] Allocated trie memory caches             clean=154.00MiB dirty=256.00MiB
INFO [07-28|23:00:19.641] Using leveldb as the backing database
INFO [07-28|23:00:19.641] Allocated cache and file handles         database=/Users/rick/go/src/test_ethereum/data/geth/chaindata cache=512.00MiB handles=5120
INFO [07-28|23:00:19.672] Using LevelDB as the backing database
INFO [07-28|23:00:19.683] Opened ancient database                  database=/Users/rick/go/src/test_ethereum/data/geth/chaindata/ancient/chain readonly=false
INFO [07-28|23:00:19.684] Disk storage enabled for ethash caches   dir=/Users/rick/go/src/test_ethereum/data/geth/ethash count=3
INFO [07-28|23:00:19.684] Disk storage enabled for ethash DAGs     dir=/Users/rick/Library/Ethash count=2
INFO [07-28|23:00:19.685] Initialising Ethereum protocol           network=1 dbversion=8
INFO [07-28|23:00:19.686]
INFO [07-28|23:00:19.687] ---------------------------------------------------------------------------------------------------------------------------------------------------------
INFO [07-28|23:00:19.687] Chain ID:  1 (mainnet)
INFO [07-28|23:00:19.687] Consensus: unknown
INFO [07-28|23:00:19.687]
INFO [07-28|23:00:19.687] Pre-Merge hard forks (block based):
INFO [07-28|23:00:19.687]  - Homestead:                   #0        (https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/homestead.md)
INFO [07-28|23:00:19.687]  - Tangerine Whistle (EIP 150): #0        (https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/tangerine-whistle.md)
INFO [07-28|23:00:19.687]  - Spurious Dragon/1 (EIP 155): #0        (https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/spurious-dragon.md)
INFO [07-28|23:00:19.687]  - Spurious Dragon/2 (EIP 158): #0        (https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/spurious-dragon.md)
INFO [07-28|23:00:19.687]  - Byzantium:                   #0        (https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/byzantium.md)
INFO [07-28|23:00:19.687]  - Constantinople:              #0        (https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/constantinople.md)
INFO [07-28|23:00:19.687]  - Petersburg:                  #0        (https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/petersburg.md)
INFO [07-28|23:00:19.687]  - Istanbul:                    #0        (https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/istanbul.md)
INFO [07-28|23:00:19.687]  - Berlin:                      #0        (https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/berlin.md)
INFO [07-28|23:00:19.687]  - London:                      #0        (https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/london.md)
INFO [07-28|23:00:19.687]
INFO [07-28|23:00:19.687] The Merge is not yet available for this network!
INFO [07-28|23:00:19.687]  - Hard-fork specification: https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/paris.md
INFO [07-28|23:00:19.687]
INFO [07-28|23:00:19.687] Post-Merge hard forks (timestamp based):
INFO [07-28|23:00:19.687]
INFO [07-28|23:00:19.687] ---------------------------------------------------------------------------------------------------------------------------------------------------------
INFO [07-28|23:00:19.687]
INFO [07-28|23:00:19.687] Loaded most recent local block           number=1048 hash=ee95d6..0a72fe td=178,852,826 age=3d11h37m
INFO [07-28|23:00:19.689] Loaded local transaction journal         transactions=0 dropped=0
INFO [07-28|23:00:19.690] Regenerated local transaction journal    transactions=0 accounts=0
WARN [07-28|23:00:19.690] Switch sync mode from snap sync to full sync
INFO [07-28|23:00:19.690] Gasprice oracle is ignoring threshold set threshold=2
WARN [07-28|23:00:19.691] Engine API enabled                       protocol=eth
WARN [07-28|23:00:19.691] Engine API started but chain not configured for merge yet
INFO [07-28|23:00:19.691] Starting peer-to-peer node               instance=Geth/v1.11.6-stable-ea9e62ca/darwin-arm64/go1.20.14
INFO [07-28|23:00:19.711] New local node record                    seq=1,753,412,617,722 id=4bc826b2752527c5 ip=127.0.0.1 udp=30303 tcp=30303
INFO [07-28|23:00:19.712] Started P2P networking                   self=enode://ce8d4b1389496968d0cbc18083963a8d82c36f66763b1d50b50e49df6884b6eaefa5464b1de69cceb9adba5d1ceefcf832ef4140b187bd009271b46832820d51@127.0.0.1:30303
INFO [07-28|23:00:19.713] IPC endpoint opened                      url=/Users/rick/go/src/test_ethereum/data/geth.ipc
INFO [07-28|23:00:19.713] Loaded JWT secret file                   path=/Users/rick/go/src/test_ethereum/data/geth/jwtsecret crc32=0xe022c3e6
INFO [07-28|23:00:19.713] HTTP server started                      endpoint=127.0.0.1:8545 auth=false prefix= cors= vhosts=localhost
INFO [07-28|23:00:19.714] WebSocket enabled                        url=ws://127.0.0.1:8551
INFO [07-28|23:00:19.714] HTTP server started                      endpoint=127.0.0.1:8551 auth=true  prefix= cors=localhost vhosts=localhost
INFO [07-28|23:00:30.080] Looking for peers                        peercount=0 tried=8 static=0
INFO [07-28|23:00:40.414] Looking for peers                        peercount=0 tried=21 static=0
```

<font style="color:rgb(31, 35, 40);">参数解释：</font>

+ <font style="color:rgb(31, 35, 40);">-datadir：主数据目录，用于存放数据库和 keystore 目录。</font>
+ <font style="color:rgb(31, 35, 40);">-networkid：指定的参数即为 chainID。</font>
+ <font style="color:rgb(31, 35, 40);">-port：TCP 监听端口，默认为 30303。</font>
+ <font style="color:rgb(31, 35, 40);">-http：开启 HTTP-RPC 功能。</font>
+ <font style="color:rgb(31, 35, 40);">-http.api：指定通过 HTTP-RPC 对外提供的 HTTP 接口列表。</font>
+ <font style="color:rgb(31, 35, 40);">-http.port：HTTP-RPC 监听接口。</font>
+ <font style="color:rgb(31, 35, 40);">-http.corsdomain：HTTP-RPC 服务接受的跨域请求域名列表，可以用逗号分隔或者使用通配符*。这在浏览器调用时是必需的。</font>
+ <font style="color:rgb(31, 35, 40);">-nodiscover：关闭节点发现功能。</font>
+ <font style="color:rgb(31, 35, 40);">-mine：开启挖矿。</font>
+ <font style="color:rgb(31, 35, 40);">-miner.threads：指定挖矿线程数。</font>
+ <font style="color:rgb(31, 35, 40);">-miner.etherbase：指定矿工奖励发放地址，需要填写上一步骤中创建的公钥地址（这是一个必填项）。</font>

#### <font style="color:rgb(31, 35, 40);">进入 JavaScript 控制台，开始与私链交互</font>
<font style="color:rgb(31, 35, 40);">刚才启动的客户端程序已经在不断的刷屏输出挖矿日志，现在请打开一个新的窗口来连接到控制台</font>

```bash
geth attach http://127.0.0.1:8999

WARN [07-28|23:04:09.281] Enabling deprecated personal namespace
Welcome to the Geth JavaScript console!

instance: Geth/v1.11.6-stable-ea9e62ca/darwin-arm64/go1.20.14
coinbase: 0x267a3c3a7d61f9e7d880e008446d435e9472ecd8
at block: 1049 (Mon Jul 28 2025 23:04:02 GMT+0800 (CST))
 datadir: /Users/rick/go/src/test_ethereum/data
 modules: admin:1.0 debug:1.0 eth:1.0 miner:1.0 net:1.0 personal:1.0 rpc:1.0 txpool:1.0 web3:1.0

To exit, press ctrl-d or type exit
-- 查看本节点下管理的账户默认只有一个，之前我自己创建了一个
> eth.accounts
["0x267a3c3a7d61f9e7d880e008446d435e9472ecd8", "0x4d92f2af3d67a00ee9d586f7cef8fe81060341be"]
>
# 挖矿奖励账户也叫coinbase
> eth.coinbase
"0x267a3c3a7d61f9e7d880e008446d435e9472ecd8"
-- 查看账户余额
> eth.getBalance("0x267a3c3a7d61f9e7d880e008446d435e9472ecd8")
734000000000000000000
> eth.getBalance("0x4d92f2af3d67a00ee9d586f7cef8fe81060341be")
1.924e+21
# 上面的余额是wei单位，现在转换为ether单位
> web3.fromWei(eth.getBalance("0x267a3c3a7d61f9e7d880e008446d435e9472ecd8"))
734
> web3.fromWei(eth.getBalance("0x4d92f2af3d67a00ee9d586f7cef8fe81060341be"))
1924
-- 交易

-- 查看区块高度
eth.blockNumber
1096

-- 启动挖矿
> miner.start()
null
>
-- 结束挖矿
> miner.stop()
null
-- 交易
> amount = web3.toWei(10, 'ether')
"10000000000000000000"
> eth.sendTransaction({from: eth.accounts[0], to:eth.accounts[1], value: amount})

# 账户每隔5min就会锁住，需要先解锁，这里要解锁的是账户0
Error: authentication needed: password or unlock
	at web3.js:6365:9(39)
	at send (web3.js:5099:62(29))
	at <eval>:1:20(15)

> personal.unlockAccount(eth.accounts[0])
# 根据错误信息得知，http方式已经不可以解锁账户
Unlock account 0x267a3c3a7d61f9e7d880e008446d435e9472ecd8
Passphrase:
GoError: Error: account unlock with HTTP access is forbidden at web3.js:6365:9(39)
	at github.com/ethereum/go-ethereum/internal/jsre.MakeCallback.func1 (native)
	at <eval>:1:23(6)

>


```

<font style="color:rgb(31, 35, 40);">也就是说，为了规避遭受攻击的风险，当节点启用 HTTP 或 WebSocket 的 RPC 连接方式时，会禁用解锁账户等一些功能。如果需要解锁账户，必须在程序启动时添加--allow-insecure-unlock 选项，或者不启用 HTTP/WS 的 RPC 连接功能。</font>

<font style="color:rgb(31, 35, 40);">如果不想启用 HTTP 方式，只需从命令中删除与-http 相关的选项即可。下面将介绍通过第一种方式来解决这个问题：</font>

<font style="color:rgb(31, 35, 40);">修改程序启动命令如下</font>

```bash
geth -datadir test_ethereum/data \
-networkid 1 \
-port "30303" \
-http \
-http.api "admin,debug,eth,miner,net,personal,txpool,web3" \
-http.port "8999" \
-http.corsdomain "*" -nodiscover \
-mine \
-miner.threads=1 \
-miner.etherbase 0x267A3C3a7D61F9e7D880E008446d435E9472ECD8 --allow-insecure-unlock



```

#### <font style="color:rgb(31, 35, 40);">重新进入 JavaScript 控制台，开始与私链交互</font>
```bash
> personal.unlockAccount(eth.accounts[0])
Unlock account 0x267a3c3a7d61f9e7d880e008446d435e9472ecd8
Passphrase:
true
-- 发起交易
> eth.sendTransaction({from: eth.accounts[0], to:eth.accounts[1], value: web3.toWei(0.5, "ether")})
"0x3b7c628a3e67cfa3ef0851d9ea192f76b7f6fd69885208698a2c62c961233cdc"  # 返回交易ID
>
# 查看交易池状态
> txpool.status
{
  pending: 1,# 有一笔待处理交易
  queued: 0
}

> web3.fromWei(eth.getBalance(eth.accounts[1]))
1924
-- 账户0 记得要开启挖矿才行 否则不交易
> web3.fromWei(eth.getBalance(eth.accounts[1]))
1924.5 # 账户1多出0.5

-- 查看交易和区块

> eth.getTransaction("0x3b7c628a3e67cfa3ef0851d9ea192f76b7f6fd69885208698a2c62c961233cdc")
{
  accessList: [],
  blockHash: "0x618919a44b09c2d2ff78a032ec5a5ea4df7fd2cd98e5d6936cdafaece25c6a2c",
  blockNumber: 1479,
  chainId: "0x1",
  from: "0x267a3c3a7d61f9e7d880e008446d435e9472ecd8",
  gas: 21000,
  gasPrice: 1000000007,
  hash: "0x3b7c628a3e67cfa3ef0851d9ea192f76b7f6fd69885208698a2c62c961233cdc",
  input: "0x",
  maxFeePerGas: 1000000014,
  maxPriorityFeePerGas: 1000000000,
  nonce: 0,
  r: "0xbbb2f2504bc63fb70c8b84d51aa7921bf6553d931dfae1cfc6d9cb40975e8751",
  s: "0x5ab00f8290aa4792bcabe1d60426fb5cbab762a92d9c22cd5e72b330681418da",
  to: "0x4d92f2af3d67a00ee9d586f7cef8fe81060341be",
  transactionIndex: 0,
  type: "0x2",
  v: "0x1",
  value: 500000000000000000
}
# 区块总高度
> eth.blockNumber
1761
# 传入整数则按照区块高度查询，字符串参数则按区块哈希查询
> eth.getBlock(1546)
{
  baseFeePerGas: 7,
  difficulty: 254333,
  extraData: "0xda83010b06846765746889676f312e32302e31348664617277696e",
  gasLimit: 14201943,
  gasUsed: 0,
  hash: "0xdcdbe13518d328e212507863b1d805a38a5994e114aab027b00d5d7fb30425f1",
  logsBloom: "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
  miner: "0x267a3c3a7d61f9e7d880e008446d435e9472ecd8",
  mixHash: "0xc566bdfdc43b0255e089225ad4c597326573a6175754addb83975057b324d094",
  nonce: "0x16c7ea3467d28673",
  number: 1546,
  parentHash: "0xe8c3687f7a76e0dc916ceac95ff72a5f8a006932a70d171d001f9c371e364564",
  receiptsRoot: "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
  sha3Uncles: "0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347",
  size: 541,
  stateRoot: "0xc5b3cb7df07bef6178d8627ebb59dcbc652dbee3b7ed39a7e3e3740c099bd81f",
  timestamp: 1753716413,
  totalDifficulty: 294003788,
  transactions: [],
  transactionsRoot: "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
  uncles: []
}


> eth.getBlock(1761)
{
  baseFeePerGas: 7,
  difficulty: 282364,
  extraData: "0xda83010b06846765746889676f312e32302e31348664617277696e",
  gasLimit: 17517795,
  gasUsed: 0,
  hash: "0xc05762ad5220b4cf34a49e7fc954ec7903dba55745ea8e3fff297e261f4b550f",
  logsBloom: "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
  miner: "0x267a3c3a7d61f9e7d880e008446d435e9472ecd8",
  mixHash: "0x9bf12529346d0b6b7a2d4076dd6ca450f058e146b24a05b8145dea29143be62b",
  nonce: "0x299463e2f0003464",
  number: 1761,
  parentHash: "0x613303e25de97e31f9cfd792e8f155f585af5a9dcd1c16122e3bfcab06c75214",
  receiptsRoot: "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
  sha3Uncles: "0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347",
  size: 542,
  stateRoot: "0x159ac4cb9d083afdd8f7981bce4e3272bf6b4702fa04f82068f743444963f2eb",
  timestamp: 1753716628,
  totalDifficulty: 351659994,
  transactions: [],
  transactionsRoot: "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
  uncles: []
}
```

