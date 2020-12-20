## 1、Tron PHP开发包概述

TronTool开发包适用于为PHP应用快速增加对Tron/USDT-TRC20数字资产的支持能力，
即支持使用自有Tron区块链节点的应用场景，也支持基于Tron官方公共API服务的
轻量级部署场景。

TronTool开发包主要包含以下特性：

- 支持Tron区块链原生Trx交易
- 支持Tron智能合约以及TRC20代币，例如USDT-TRC20等
- 支持交易的离线签名，避免泄露私钥
- 完善的Tron节点API封装，支持全节点、Solidity节点和事件节点提供的API
- 支持使用自有节点或第三方节点，例如Tron官方提供的公共节点

TronTool软件包运行在**Php 7.1+**环境下，当前版本1.0.0，主要类/接口及关系如下图所示：

![trontool arch](http://sc.hubwiz.com/codebag/tron-php-lib/img/trontool-arch.png)


TronTool的主要代码文件清单如下：

<table class="table table-striped">
  <thead>
    <tr><th>代码文件</th><th>说明</th></tr>
  </thead>
  <tbody>
    <tr><td>tron.php/src/TronKit.php</td><td>Tron开发包入口类</td></tr>
    <tr><td>tron.php/src/Trc20.php</td><td>Tron TRC20智能合约封装类</td></tr>
    <tr><td>tron.php/src/Contract.php</td><td>Tron智能合约封装类</td></tr>
    <tr><td>tron.php/src/Credential.php</td><td>Tron区块链身份标识类，用于交易签名</td></tr>
    <tr><td>tron.php/src/Address.php</td><td>Tron地址表示类</td></tr>
    <tr><td>tron.php/src/TronApi.php</td><td>Tron节点API聚合封装类</td></tr>
    <tr><td>tron.php/src/NodeClient.php</td><td>HTTP协议封装类</td></tr>
    <tr><td>demo/NewAddressDemo.php</td><td>演示代码，创建新的Tron区块链地址</td></tr>
    <tr><td>demo/TrxDemo.php</td><td>演示代码，Trx转账交易及余额查询</td></tr>
    <tr><td>demo/Trc20Demo.php</td><td>演示代码，Trc20代币转账、余额查询、事件监听等</td></tr>
    <tr><td>demo/DeployContractDemo.php</td><td>演示代码，智能合约的部署</td></tr>
    <tr><td>demo/build-contract.php</td><td>示例Trc20代币合约的构建脚本</td></tr>
    <tr><td>demo/contract/EzToken.sol</td><td>示例Trc20代币合约</td></tr>
    <tr><td>demo/contract/build/EzToken.abi</td><td>示例Trc20代币合约的ABI文件</td></tr>
    <tr><td>demo/contract/build/EzToken.bin</td><td>示例Trc20代币合约的字节码文件</td></tr>
    <tr><td>vendor</td><td>第三方依赖包目录</td></tr>
    <tr><td>composer.json</td><td>composer配置文件</td></tr>
  </tbody>
</table>

## 2、使用示例代码

### 2.1 创建新地址

在终端进入演示代码目录，执行如下命令：

```
~$ cd ~/trontool/demo
~/trontool/demo$ php NewAddressDemo.php
```

执行结果如下：

![tron php uml](http://sc.hubwiz.com/codebag/tron-php-lib/img/new-address-demo.png)

### 2.2 Trx转账及余额查询

在终端进入演示代码目录，执行如下命令：

```
~$ cd ~/trontool/demo
~/trontool/demo$ php TrxDemo.php
```

执行结果如下：

![tron php trx demo](http://sc.hubwiz.com/codebag/tron-php-lib/img/trx-demo.png)

### 2.3 Trc20代币转账、余额查询及事件监听

在终端进入演示代码目录，执行如下命令：

```
~$ cd ~/trontool/demo
~/trontool/demo$ php Trc20Demo.php
```

执行结果如下：

![tron php trc20 demo](http://sc.hubwiz.com/codebag/tron-php-lib/img/trc20-demo.png)

### 2.4 Tron智能合约部署

在终端进入演示代码目录，执行如下命令：

```
~$ cd ~/trontool/demo
~/trontool/demo$ php DeployContractDemo.php
```

执行结果如下：

![tron php deploy contract demo](http://sc.hubwiz.com/codebag/tron-php-lib/img/deploy-contract-demo.png)

## 2、使用TronKit

TronKit是开发包的入口，使用这个类可以快速实现如下功能：

- Trx转账与余额查询
- Trc20代币转账、授权、余额查询等

### 2.1 实例化TronKit

TronKit实例化需要传入`TronApi`对象和`Credential`对象，这两个
参数分别封装了Tron节点提供的API，以及进行交易签名的用户身份信息。

例如，下面的代码创建一个接入Tron主链的TronKit实例，并使用
指定的私钥进行交易签名：

```
use TronTool\TronKit;
use TronTool\TronApi;
use TronTool\Credential;

$kit = new TronKit(
  TronApi::mainNet(),                                       //接入主链
  Credential::fromPrivateKey('87c12d....d435')              //使用指定私钥
);
```

### 2.2 Trx转账及余额查询

使用TronKit的`sendTrx()`方法进行Trx转账，例如发送1000 TRX：

```
$to = 'TDN3QY85Jft3RwgyatjRNmrwRmwkn8qwqx';                 //转账目标地址
$amount = 1000000000;                                       //转账金额，单位：SUN
$ret = $kit->sendTrx($to,$amount);                          //提交Trx转账交易
echo 'txid => ' . $ret->tx->txID .  PHP_EOL;                //显示交易ID
echo 'result => ' . $ret->result . PHP_EOL;                 //显示交易结果
```

注意：需要将金额单位转换为SUN，1 TRX = 1000000 SUN。

使用`getTrxBalance()`方法查询指定地址的Trx余额，例如：

```
$addr = 'TDN3QY85Jft3RwgyatjRNmrwRmwkn8qwqx';               //要查询的Tron地址
$balance = $kit->getTrxBlanace($addr);                      //查询Trx余额，单位：SUN
echo 'trx balance => ' . $balance . PHP_EOL;                //显示余额
```

### 2.3 TRC20代币转账

使用`Trc20()`方法获取指定TRC20代币合约实例，然后调用合约
的`transfer()`方法进行TRC20代币转账。例如，下面的代码
指定地址间转账1315300个最小单位的USDT-TRC20代币，即
1.3153 USDT：

```
$to = 'TDN3QY85Jft3RwgyatjRNmrwRmwkn8qwqx';                 //转账目标地址
$amount = 1315300;                                          //转账Trc20代币数量
$contractAddress = 'TR7NHqjeKQxGTCi8q8ZY4pL8otSzgjLj6t'     //USDT-TRC20代币合约的部署地址
$usdt = $kit->Trc20($contractAddress);                      //创建Trc20代币合约实例
$ret = $usdt->transfer($to,$amount);                        //转账Trc20代币
echo 'txid => ' . $ret->tx->txID .  PHP_EOL;                //显示转账交易ID
echo 'result => ' . $ret->result . PHP_EOL;                 //显示转账交易结果
```

### 2.4 TRC20代币余额查询

使用`Trc20()`方法获取指定TRC20代币合约实例，然后调用合约
的`balanceOf()`方法查询指定地址的TRC20代币余额。例如，
下面的代码查询指定地址的USDT代币余额：

```
$usdt = $kit->Trc20('TR7NHqjeKQxGTCi8q8ZY4pL8otSzgjLj6t');          //创建USDT-TRC20代币合约实例
$balance = $usdt->balanceOf('TDN3QY85Jft3RwgyatjRNmrwRmwkn8qwqx');  //查询Trc20代币余额
echo 'usdt balance => ' . $balance . PHP_EOL;                       //显示代币余额
```

### 2.5 TRC20代币事件查询

使用`Trc20()`方法获取指定TRC20代币合约实例，然后调用
合约的`events()`方法查询指定合约触发事件。

例如查询USDT代币合约最近10秒的事件：

```
$usdt = $kit->Trc20('TR7NHqjeKQxGTCi8q8ZY4pL8otSzgjLj6t');    //创建Trc20代币合约实例
$since = time() - 10000;                                      //计算检查时间点
$events = $usdt->events($since);                              //提取合约事件
foreach($events as $event){                                   
  echo 'block height => ' . $event->block_number . PHP_EOL;   //显示事件触发的区块高度
  echo 'event name => ' . $event->event_name . PHP_EOL;       //显示事件名称
}  
```

`events()`返回的结果是一个事件对象数组，每个成员对象的主要字段
说明如下：

- caller_contract_address：调用合约地址，base58格式
- transaction_id：触发合约事件的交易ID，16进制字符串
- result：合约事件参数列表，数组
- result_type：合约事件参数类型列表，数组
- block_timestamp：事件所在区块时间戳，整数
- block_number：事件所在区块号，整数
- event_name：事件名称，字符串
- contract_address：合约地址，base58格式
- event_index：事件索引序号，整数

例如，下面是一个TRC20代币合约的Transfer事件对象的JSON表示，
在event_name字段给出了事件名称，在result字段则给出了两种索引
形式的事件参数：

```
{
  "caller_contract_address": "TS2Hzo6KpAc8Ym2nGb3idpMtUpM2GiK2gL",
  "transaction_id": "265cf378f4943b7c77b7a294f533d4b8c718c297dd28a664848d77cd3f3a0af0",
  "result": {
    "0": "0x2539ef4f3eb733c105a957eebb20fd60ad8c9a43",      //事件参数0
    "1": "0x6f6794f3904ff51f9fa81e928afdec91f6744a50",      //事件参数1
    "2": "8",                                               //事件参数2        
    "_from": "0x2539ef4f3eb733c105a957eebb20fd60ad8c9a43",  //事件参数_from
    "_value": "8",                                          //事件参数_value
    "_to": "0x6f6794f3904ff51f9fa81e928afdec91f6744a50"     //事件参数_to
  },
  "result_type": {
    "_from": "address",                                     
    "_value": "uint256",
    "_to": "address"
  },
  "block_timestamp": 1586263455000,
  "block_number": 3539438,
  "event_name": "Transfer",                                 //事件名称   
  "contract_address": "TS2Hzo6KpAc8Ym2nGb3idpMtUpM2GiK2gL",
  "event_index": 0
}
```

### 3、Tron区块链身份与地址表示

在TronTool中，使用`Credential`表征Tron区块链中的一个用户身份，使用`Address`
表征Tron区块链中的一个地址。两者的区别在于Credential包含了用户
的私钥信息，可以用来签名交易，因此需要保护，而Address则是可以
公开的信息。

使用Credential类的静态方法`create()`创建新账户。例如，下面的代码创建一个
新的账户并显示其私钥、公钥和地址：

```
use TronTool\Credential;

$credential = Credential::create();                           //创建新账号
echo 'private key => ' . $credential->privateKey() . PHP_EOL; //显示私钥
echo 'public key => ' . $credential->publicKey() . PHP_EOL;   //显示公钥
echo 'address => ' . $credential->address() . PHP_EOL;        //显示地址
```

可以使用静态方法`fromPrivateKey()`导入已有的私钥来实例化Credential。
例如下面的代码导入已有私钥并显示地址：

```
use TronTool\Credential;

$credential = Credential::fromPrivateKey('7889...023a');      //导入已有私钥
echo 'address => ' . $credential->address() . PHP_EOL;        //显示相应地址
```

在Tron区块链中，地址有两种表示：16进制和base58表示，例如
下面是同一个地址的两种表示：

- base58：TDN3QY85Jft3RwgyatjRNmrwRmwkn8qwqx
- 16进制: 412539EF4F3EB733C105A957EEBB20FD60AD8C9A43

Address类包含了相应的编解码逻辑，可以方面的利用不同形式
的地址实例化Address。例如：

```
$a1 = Address::fromBase58('TDN3QY85Jft3RwgyatjRNmrwRmwkn8qwqx');
echo $a1->hex() . PHP_EOL;        //输出：412539EF4F3EB733C105A957EEBB20FD60AD8C9A43

$a2 = Address::fromHex('412539EF4F3EB733C105A957EEBB20FD60AD8C9A43');
echo $a2->base58() . PHP_EOL;     //输出：TDN3QY85Jft3RwgyatjRNmrwRmwkn8qwqx
```

有时我们只需要简单的在base58和16进制之间转换地址，这时并
不需要中间的Address对象，可以直接使用静态方法encode()和decode()。
例如：

```
$a1 = Address::decode('TDN3QY85Jft3RwgyatjRNmrwRmwkn8qwqx');
echo $a1 . PHP_EOL;             //输出：412539EF4F3EB733C105A957EEBB20FD60AD8C9A43
$a2 = Address::encode('412539EF4F3EB733C105A957EEBB20FD60AD8C9A43');
echo $a2 . PHP_EOL;             //输出：TDN3QY85Jft3RwgyatjRNmrwRmwkn8qwqx
```

## 4、使用TronApi访问Tron节点API

使用TronApi访问Tron的各种节点API。TronApi聚合了多种Tron节点
提供的API，例如tron全节点、solidity节点和事件服务节点的API。

实例化TronApi时，可以分别为不同类型的Tron节点指定不同的连接URL，
例如：

```
use TronTool\TronApi;

$tc = new TronApi(
  'https://api.trongrid.io',       //全节点URL
  'https://api.trongrid.io',       //合约节点URL
  'https://api.trongrid.io'        //事件节点URL
);
```

当上述三个节点的URL相同时，可以简写为：

```
$tc = new TronApi('https://api.trongrid.io');
```

如果用的是Tron官方提供的TronGrid节点，那么可以直接使用TronApi
提供的两个静态函数mainNet()和testNet()，分别接入主链和shasta测试链。

例如，下面的代码是等效的：

```
$tc = new TronApi('https://api.trongrid.io');
$tc = TronApi::mainNet();                       //与上面等效

$tc = new TronApi('https://api.shasta.trongrid.io');
$tc = TronApi::testNet();                       //与上面等效
```

TronApi封装了Tron官方多种节点提供的API，并基本保持了对应关系以
便于查找利用。例如查询账户的TRX余额：

```
$info = $tc->getAccount('TEgM5CPeqowkKUXoKrFrpvB7vcBgVkD4tP');  //查询账户信息
echo 'balance -> ' . $info->balance . PHP_EOL;                  //显示账户余额  
```

