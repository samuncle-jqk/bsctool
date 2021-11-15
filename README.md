[链接](http://sc.hubwiz.com/codebag/bsctool-php/)

为PHP应用快速增加币安智能链BNB/BEP20数字资产支持能力，同时支持使用自己的节点或第三方服务API。

1、开发包概述
-------

BscTool开发包适用于为PHP应用快速增加对币安智能链BNB/BEP20数字资产的支持能力， 即支持使用自有BSC区块链节点的应用场景，也支持基于BSC区块链官方节点API服务的 轻量级部署场景。

BscTool开发包主要包含以下特性：

*   支持BSC区块链原生BNB转账交易及余额查询
*   支持BSC链上智能合约的部署与交互，支持BEP20代币转账交易及到账跟踪
*   支持BSC链上交易的离线签名，避免泄露私钥
*   支持使用自有节点或第三方节点，例如BSC官方提供的公共节点

BscTool软件包运行在 **Php 7.1+** 环境下，当前版本1.0.0，主要类/接口及关系如下图所示：

![bsctool uml](img/bsctool-uml.png)

BscTool的主要代码文件清单如下：

代码文件

说明

bsc.php/src/Kit.php

BscTool开发包入口类

bsc.php/src/Bep20.php

BEP20智能合约封装类

bsc.php/src/SmartContract.php

BSC智能合约封装类

bsc.php/src/Credential.php

BSC区链上身份标识类，用于交易签名

bsc.php/src/NodeClient.php

BSC节点协议封装类

bsc.php/src/Callback.php

Php回调辅助类

bsc.php/src/Helper.php

杂项辅助函数集

contracts/WizToken.sol

示例BEP20代币合约

bin/build-contracts.sh

合约编译脚本工具

demo/credential-demo.php

演示如何创建新的BSC账号或导入已有私钥

demo/bnb-demo.php

演示BNB转账及余额查询

demo/bep20-demo.php

演示BEP20代币转账及余额查询

demo/bep20-event-demo.php

演示BEP20代币到账监听

demo/deploy-contract-demo.php

演示代码，智能合约的部署

vendor

第三方依赖包目录

composer.json

composer配置文件

2、使用示例代码
--------

在使用示例代码之前，请按照实际情况设置`demo/config.php`中的以下配置信息：

*   NETWORK：要接入的区块链网络，可选：mainNet - BSC主链， testNet - BSC测试链
*   ALICE\_ADDR：demo程序使用的主测试账号，该账号将用于部署代币合约、执行BNB及BEP20代币转账交易等操作，因此 需要有一定数量的BNB余额。
*   ALICE\_SK：主测试账号对应的私钥

### 2.1 BSC链上地址创建与恢复

`demo/credential-demo.php`演示了如何使用BscTool创建新的BSC链上地址，或者导入已有私钥重建账号。

在终端进入演示代码目录，执行如下命令：

    ~$ cd ~/bsctool/demo
    ~/bsctool/demo$ php credential-demo.php
    

执行结果如下：

![](img/credential-demo.png)

### 2.2 BNB转账及余额查询

`demo/bnb-demo.php`演示了如何使用BscTool实现BNB转账和余额查询。

在终端进入演示代码目录，执行如下命令：

    ~$ cd ~/bsctool/demo
    ~/bsctool/demo$ php bnb-demo.php
    

执行结果如下：

![](img/bnb-demo.png)

### 2.3 BSC智能合约部署

`demo/deploy-contract-demo.php`演示了如何使用BscTool部署EVM智能合约。

在终端进入演示代码目录，执行如下命令：

    ~$ cd ~/bsctool/demo
    ~/bsctool/demo$ php deploy-contract-demo.php
    

执行结果如下：

![](img/deploy-contract-demo.png)

### 2.4 BEP20代币转账及余额查询

`demo/bep20-demo.php`演示了如何使用BscTool实现BEP20代币转账和余额查询等操作。

在终端进入演示代码目录，执行如下命令：

    ~$ cd ~/bsctool/demo
    ~/bsctool/demo$ php bep20-demo.php
    

执行结果如下：

![](img/bep20-demo.png)

### 2.5 BEP20代币到账跟踪

`demo/bep20-event-demo.php`演示了如何使用BscTool的合约事件查询功能 实现BEP20代币的到账跟踪。

在终端进入演示代码目录，执行如下命令：

    ~$ cd ~/bsctool/demo
    ~/bsctool/demo$ php bep20-event-demo.php
    

执行结果如下：

![](img/bep20-event-demo.png)

3、使用BscTool
-----------

Kit类是BscTool开发包的入口，使用这个类可以快速实现如下功能：

*   BNB转账与余额查询
*   BEP20代币转账、授权、余额查询等

### 3.1 Kit的实例化

Kit实例化需要传入`NodeClient`对象和`Credential`对象，这两个 参数分别封装了BSC节点提供的API、以及进行交易签名的用户身份信息。

例如，下面的代码创建一个接入BSC主链的Kit实例，并使用 指定的私钥进行交易签名：

    //use bsctool\Kit;
    //use bsctool\NodeClient;
    //use bsctool\Credential;
    
    $kit = new Kit(
      NodeClient::mainNet(),                                    //接入主链
      Credential::fromKey('0x87c12d....d435')                   //使用指定私钥
    );
    

### 3.2 BNB转账及余额查询

使用Kit对象的`transfer()`方法进行BNB转账，例如发送0.1 BNB：

    //use bsctool\Kit;
    
    $to = '0x90F8bf6...0e7944Ea8c9C1';                          //转账目标地址
    $amount = bn('100000000000000000');                         //转账金额，按最小单位
    $txid = $kit->transfer($to,$amount);                        //提交BNB转账交易
    echo 'txid => ' . $txid .  PHP_EOL;                         //显示交易ID
    

注意：需要将金额转换为最小单位，由于BNB的小数位数是18， 因此 0.1 BNB = 100000000000000000 最小单位。

使用`balanceOf()`方法查询指定地址的BNB余额，例如：

    $addr = '0x90F8bf6...0e7944Ea8c9C1';                  //要查询的BSC链上地址
    $balance = $kit->balanceOf($addr);                    //查询BNB余额，按最小单位
    echo 'balance => ' . $balance . PHP_EOL;              //显示BNB余额
    

### 3.3 BEP20代币转账

使用Kit对象的`bep20()`方法获取指定BEP20代币合约实例，然后调用合约 的`transfer()`方法进行BEP20代币转账。例如，下面的代码 指定地址间转账123.4567 BUSD-T（代币合约地址：0x55d398326f99059ff775485246999027b3197955）：

    //use bsctool\Kit;
    
    $to = 'TDN3QY85Jft3RwgyatjRNmrwRmwkn8qwqx';                   //转账目标地址
    $amount = bn('123456700000000000000');                        //转账BEP20代币数量
    $contractAddr = '0x55d398326f99059ff775485246999027b3197955'  //BUSD-T代币合约的部署地址
    $txid = $kit->bep20($contractAddr)
                ->transfer($to,$amount);                          //转账BEP20代币
    echo 'txid => ' . $txid .  PHP_EOL;                           //显示转账交易ID
    

### 3.4 BEP20代币余额查询

使用`bep20()`方法获取指定的BEP20代币合约实例，然后调用合约 的`balanceOf()`方法查询代币余额。例如，下面的代码查询指定地址 的BUSD-T代币余额：

    //use bsctool\Kit;
    
    $contractAddr = '0x55d398326f99059ff775485246999027b3197955'  //BUSD-T代币合约的部署地址
    $balance = $kit->bep20($contractAddr)
                   ->balanceOf('0x90F8bf6...0e7944Ea8c9C1');      //查询地址0x90F8...的代币余额
    echo 'balance => ' . $balance . PHP_EOL;                      //显示代币余额
    

### 3.5 BEP20代币到账跟踪

使用`bep20()`方法获取指定BEP20代币合约实例，然后调用 合约实例的`getTransferEvents()`方法查询指定条件的转账事件。

可以使用`getTransferEvents()`方法跟踪指定地址的到账状态。 例如查询地址0x90F8...在最新区块的BUSD-T代币到账事件：

    //use bsctool\Kit;
    
    $contractAddr = '0x55d398326f99059ff775485246999027b3197955'  //BUSD-T代币合约的部署地址
    $events = $kit->bep20($contractAddr)
                  ->getTransferEvents(             //查询到账事件
                    [],                            //转出账号，空数组表示不要求特定的转出账号 
                    ['0x90F8bf6...0e7944Ea8c9C1'], //接收账号，仅查询地址0x90F8...的到账事件
                    'latest',                      //查询起始区块号，latest表示使用最新区块
                    'latest'                       //查询结束区块号，latest表示使用最新区块
                  );                              
    
    foreach($events as $event){                                   
      echo 'block => ' . $event->blockNumber . PHP_EOL;            //事件发生区块号
      echo 'from => ' . $event->params['from'] . PHP_EOL;          //转出账号
      echo 'to => ' . $event->params['to'] . PHP_EOL;              //转入账号
      echo 'value => ' . $event->params['value'] . PHP_EOL;        //转账金额
    }
    

`getTransferEvents()`方法返回的结果是一个事件对象数组，每个成员对象的主要字段 说明如下：

*   blockHash：事件触发的区块哈希
*   blockNumber：事件触发的区块号
*   transactionHash：触发事件的交易ID
*   address：事件触发的合约地址
*   name：事件名称，例如转账事件的名称为：Transfer
*   params：事件参数数组，例如转账事件包含以下三个参数：
    *   from：转出账号
    *   to：转入账号
    *   value：转账数量

4、BSC区块链身份与地址表示
---------------

在BscTool中，使用`Credential`对象表征BSC区块链中的一个用户身份，使用普通的 字符串表征BSC区块链中的一个地址，这两者的区别在于Credential包含了用户 的私钥信息，可以用来签名交易，因此需要保护。

使用Credential类的静态方法`create()`创建新账户。例如，下面的代码创建一个 新的账户并显示其私钥、公钥和地址：

    //use bsctool\Credential;
    
    $credential = Credential::create();                              //创建新账号
    echo 'private key => ' . $credential->getPrivateKey() . PHP_EOL; //显示私钥
    echo 'public key => ' . $credential->getPublicKey() . PHP_EOL;   //显示公钥
    echo 'address => ' . $credential->getAddress() . PHP_EOL;        //显示地址
    

可以使用静态方法`fromKey()`导入已有的私钥来实例化Credential。 例如下面的代码导入已有私钥并显示地址：

    //use bsctool\Credential;
    
    $credential = Credential::fromKey('0x7889...023a');           //导入已有私钥
    echo 'address => ' . $credential->getAddress() . PHP_EOL;        //显示相应地址
    

5、使用NodeClient
--------------

NodeClient类封装了BSC节点的RPC访问协议。实例化NodeClient时，需要指定要连接 的节点URL，例如使用本地的全节点：

    //use bsc\NodeClient;
    
    $client = new NodeClient('http://localhost:8545');
    

当使用BSC官方节点时，NodeClient类也提供列两个静态函数`mainNet()`和`testNet()`，分别 用于接入官方提供的主链节点和测试链节点。

例如，下面的代码是等效的：

    //use bsctool\NodeClient;
    
    $client = new NodeClient('https://bsc-dataseed.binance.org/');
    $tc = NodeClient::mainNet();                       //与上面等效
    
    $tc = new NodeClient('https://data-seed-prebsc-1-s1.binance.org:8545/');
    $tc = NodeClient::testNet();                       //与上面等效
    
