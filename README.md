

# ABI

应用程序二进制接口

是指两个程序模块之间的接口，通常，一个在操作系统层面，另外一个在用户程序层面。ABI定义了数据结构和函数如何在机器指令中被访问。

# 一、数据类型

## 1.1 布尔型（bool）

布尔值true或false，可以使用逻辑操作符！（否）、&&（与）、||（或）、==（等于）和！=（不等于）。

##1.2  整数型（int、uint）

整数型（int、uint）有符号（int）和无符号（uint）整数，以8比特为单位，从int8到uint256。当没有明确说明长度时，默认使用256比特，因为这也是以太坊虚拟机的字长。

## 1.3 固定浮点数（fixed、ufixed）

固定小数点的浮点数，使用(u)fixedMxN定义，其中M是比特的位数（从8到256）, N用来表示小数点之后有多少位（最多18），例如ufixed32x2。

## 1.4 地址

20字节长度的以太坊地址。这个address对象有若干很有用的成员函数，最主要的就是balance，用来返回地址上以太币的余额，还有transfer，用来向地址发送以太币。

## 1.5 字节数组（固定的）

固定长度的字节数组，使用bytes1到bytes32进行声明。

##1.6 字节数组（动态的）

动态长度的字节数组，使用bytes或string进行声明。

## 1.7 枚举

用户定义的数据类型，用于枚举互相没有关联的一组值，如enumNAME {LABEL1, LABEL 2, ...}。

## 1.8 数组

数组可以包含任意类型的数据，可以是固定的，也可以是动态的。例如，uint32[][5]包含五个动态数组，成员为无符号整型。

## 1.9 结构

由用户定义的一种数据结构，其中包含多种其他类型的变量，如struct NAME{TYPE1 VARIABLE1; TYPE2VARIABLE2; ...}。

## 1.10 映射

 用于查找key=>value映射的哈希表，如mapping(KEY_TYPE=>VALUE_TYPE) NAME。除了上述数据类型，Solidity也提供了多种可用于计算不同单位的字面量。

## 1.11 时间单位

seconds、minutes、hours和days可以作为前缀，用来对基本的计时单位seconds进行单位转换。

## 1.12以太币单位

wei、finney、szabo和ether可以作为前缀，对基本的以太币单位wei进行单位转换。

# 二、预定义的全局变量和函数

## 2.1 交易调用上下文

msg对象是指触发合约执行的交易，这个交易可能来自外部账户交易调用，也可能来自其他合约的消息调用。这个对象包含了一些有用的属性：

### 2.1.1 msg.sender 

发起合约调用的以太坊地址，这个地址不一定是发起调用的外部账户的地址。

如果合约是由来自外部账户的交易触发的，那么这个地址就是对交易进行签名的地址，否则就是另外一个合约的地址（合约之间的互相调用）。

### 2.1.2 msg.value

调用中发送的以太币数量（以wei为单位）。

### 2.1.3 msg.gas

执行环境中当前可用的gas数量。这个属性已经被废弃，将会被Solidity v0.4.21中的gasleft函数取代。

### 2.1.4 msg.data

调用合约时传入的数据。

### 2.1.5 msg.sig

传输数据的前四个字节，这是一个函数选择器。

当一个合约调用另外一个合约时，msg对象的值将会全部发生变化，用以体现出新的调用方的信息。**唯一的例外是delegatecall函数**，它在当前合约的上下文中执行其他合约或者库的代码。

## 2.2 交易的上下文

tx对象提供了访问交易相关信息的办法：

### 2.2.1 tx.gasprice

调用交易的gas价格。

### 2.2.2 tx.origin

发起这个交易的外部账户的地址。警告：这是个不安全的做法。

## 2.3 区块的上下文

block对象包含了当前区块的信息：

### 2.3.1 block.blockhash(blockNumber)

指定区块的哈希值，仅限于当前区块之前不超过256个区块。目前已经弃用，在Solidity v.0.4.22中被blockhash方法所取代。

### 2.3.2 block.coinbase

当前区块的矿工地址，用于接收这个区块的出块奖励和所有交易费。

### 2.3.3 block.difficulty

当前区块工作量证明的难度。

### 2.3.4 block.gaslimit

可以在当前区块中包含的所有事务中花费的最大gas数量。

### 2.3.5 block.number

当前的区块编号（区块在链中的高度）。

### 2.3.6 block.timestamp

由矿工写入的当前区块的时间戳，使用的是UNIX式的计时方式。

## 2.4 地址对象

不论是来自于外部输入，还是从合约对象中获取，地址类对象都包含如下属性和方法：

### 2.4.1 address.balance

当前地址的余额，以wei为单位。例如，当前合约账户的余额可以用如下方式来获得：address(this).balance。

### 2.4.2 address.transfer(amount)

转账一定数量（以wei为单位）的以太币到指定的地址，遇到任何错误都将抛出异常。我们在Faucet例子中使用过这个方法，针对的是msg.sender这个地址，即msg.sender.transfer。

### 2.4.3 address.send(amount)

跟上面的transfer方法类似，但是在遇到错误时不会抛出异常，而是返回false。警告：需要总是检查send方法的返回值（确保转账成功）。

### 2.4.4 address.call(payload)

一种底层CALL函数，可以构建一个包含自定义数据的调用，出错时会返回false。警告：这不是一种安全的调用，调用接收方可以无意或有意地耗尽你的gas，导致合约因为00G异常而停止，总是要检查call的返回值。

### 2.4.5 address.callcode(payload)

一种底层CALLCODE函数，类似address(this).call(...)，但是this由这个合约的代码替代，在出错时会返回false。警告：仅限于特殊使用场合。address.delegatecall()一种底层DELEGATECALL函数，与callcode(...)相似，但是当前合约可以访问完整的msg上下文。在出错时会返回false。警告：仅限于特殊使用场合。



## 2.5 内建函数

其他值得注意的函数：

addmod、mulmod

模数的加法和乘法，例如：addmod(x,y,k)计算(x+y)%k。

keccak256、sha256、sha3、ripemd160

多种算法的哈希计算函数。ecrecover从数字签名的数据中计算，获取签名地址。

selfdestrunct(recipient_address)

删除当前的合约，把合约中剩余的比特币转账到recipient_address

# 三、合约定义

interface

接口的定义方式几乎跟合约完全相同，不过这些函数并没有具体的定义，只是做了声明而已。这些类型函数的声明通常被称为桩，因为它可以在不进行任何实现的情况下，告诉人们这些函数接收的参数和返回的值。这用于定义合约的接口，如果接口被继承，那么接口声明的所有函数必须在继承的子合约中定义。

library

库合约意味着只被部署一次，并供其他合约调用。使用delegatecall方法可以调用库合约。

## 货币合约（Subcurrency）示例

```solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity  >=0.7.0 <0.9.0;

contract Coin {
    // 关键字“public”让这些变量可以从外部读取
    address public minter;
    mapping (address => uint) public balances;

    // 轻客户端可以通过事件针对变化作出高效的反应
    event Sent(address from, address to, uint amount);

    // 这是构造函数，只有当合约创建时运行
    constructor() {
        minter = msg.sender;
    }

    function mint(address receiver, uint amount) public {
        require(msg.sender == minter);
        require(amount < 1e60);
        balances[receiver] += amount;
    }

    function send(address receiver, uint amount) public {
        require(amount <= balances[msg.sender], "Insufficient balance.");
        balances[msg.sender] -= amount;
        balances[receiver] += amount;
        emit Sent(msg.sender, receiver, amount);
    }
}
```

`address public minter;` 这一行声明了一个可以被公开访问的 `address` 类型的状态变量。 `address` 类型是一个160位的值，且不允许任何算数操作。这种类型适合存储合约地址或外部人员的密钥对。**关键字 `public` 自动生成一个函数，允许你在这个合约之外访问这个状态变量的当前值。**如果没有这个关键字，其他的合约没有办法访问这个变量。由编译器生成的函数的代码大致如下所示（暂时忽略 external 和 view）：

```solidity
function minter() external view returns (address) { return minter; }
```

下一行， `mapping (address => uint) public balances;` 也创建一个公共状态变量，但它是一个更复杂的数据类型。 该类型将address映射为无符号整数。

 Mappings 可以看作是一个 [哈希表](https://en.wikipedia.org/wiki/Hash_table) 它会执行虚拟初始化，以使所有可能存在的键都映射到一个字节表示为全零的值。

 但是，这种类比并不太恰当，因为它既不能获得映射的所有键的列表，也不能获得所有值的列表。

 因此，要么记住你添加到mapping中的数据（使用列表或更高级的数据类型会更好），要么在不需要键列表或值列表的上下文中使用它，就如本例。 而由 `public` 关键字创建的getter函数 [getter function](https://learnblockchain.cn/docs/solidity/contracts.html#getter-functions) 则是更复杂一些的情况， 它大致如下所示：

```solidity
function balances(address _account) external view returns (uint) {
    return balances[_account];
}
```

正如你所看到的，你可以通过该函数轻松地查询到账户的余额。

`event Sent(address from, address to, uint amount);` 这行声明了一个所谓的“事件（event）”，**它会在 `send` 函数的最后一行被发出。**

用户界面（当然也包括服务器应用程序）可以监听区块链上正在发送的事件，而不会花费太多成本。

一旦它被发出，监听该事件的listener都将收到通知。

而所有的事件都包含了 `from` ， `to` 和 `amount` 三个参数，可方便追踪交易。

 为了监听这个事件，你可以使用如下JavaScript代码（假设 Coin 是已经通过 [web3.js 创建好的合约对象](https://learnblockchain.cn/docs/web3js-0.2x/web3.eth.html#contract) ）：

```javascript
Coin.Sent().watch({}, '', function(error, result) {
    if (!error) {
        console.log("Coin transfer: " + result.args.amount +
            " coins were sent from " + result.args.from +
            " to " + result.args.to + ".");
        console.log("Balances now:\n" +
            "Sender: " + Coin.balances.call(result.args.from) +
            "Receiver: " + Coin.balances.call(result.args.to));
    }
})
```

这里请注意自动生成的`balance`函数是如何从用户界面调用的。

特殊函数`constructor`是仅在创建合约期间运行的构造函数，不能再时候调用。

最后，真正被用户或其他合约所调用的，以完成本合约功能的方法是 `mint` 和 `send`。 如果 `mint` 被合约创建者外的其他人调用则什么也不会发生。 另一方面， **`send` 函数可被任何人用于向他人发送币 (当然，前提是发送者拥有这些币)。记住，如果你使用合约发送币给一个地址，当你在区块链浏览器上查看该地址时是看不到任何相关信息的。因为，实际上你发送币和更改余额的信息仅仅存储在特定合约的数据存储器中。通过使用事件，你可以非常简单地为你的新币创建一个“区块链浏览器”来追踪交易和余额。**

## 投票合约

我们的想法是为每个（投票）表决创建一份合约，为每个选项提供简称。 然后作为合约的创造者——即主席，将给予每个独立的地址以投票权。

地址后面的人可以选择自己投票，或者委托给他们信任的人来投票。

在投票时间结束时，`winningProposal()` 将返回获得最多投票的提案。

 
## 投票合约

我们的想法是为每个（投票）表决创建一份合约，为每个选项提供简称。 然后作为合约的创造者——即主席，将给予每个独立的地址以投票权。

地址后面的人可以选择自己投票，或者委托给他们信任的人来投票。

在投票时间结束时，`winningProposal()` 将返回获得最多投票的提案。

 

## 简单的公开拍卖

以下简单的拍卖合约的总体思路是每个人都可以在投标期内发送他们的出价。 出价已经包含了资金/以太币，来将投标人与他们的投标绑定。 如果最高出价提高了（被其他出价者的出价超过），之前出价最高的出价者可以拿回她的钱。 在投标期结束后，受益人需要手动调用合约来接收他的钱 - 合约不能自己激活接收。

```
对于能接收以太币的函数，关键字 payable 是必须的。
```

## 秘密竞拍（盲拍）

秘密竞拍的好处是在投标结束前不会有时间压力。 在一个透明的计算平台上进行秘密竞拍听起来像是自相矛盾，但密码学可以实现它。



在 **投标期间** ，投标人实际上并没有发送她的出价，而只是发送一个哈希版本的出价。 由于目前几乎不可能找到两个（足够长的）值，其哈希值是相等的，因此投标人可通过该方式提交报价。 在投标结束后，投标人必须公开他们的出价：他们不加密的发送他们的出价，合约检查出价的哈希值是否与投标期间提供的相同。

另一个挑战是如何使拍卖同时做到 **绑定和秘密** : 唯一能阻止投标者在她赢得拍卖后不付款的方式是，让她将钱连同出价一起发出。 但由于资金转移在以太坊中不能被隐藏，因此任何人都可以看到转移的资金。

下面的合约通过接受任何大于最高出价的值来解决这个问题。 当然，因为这只能在披露阶段进行检查，有些出价可能是 **无效** 的， 并且，这是故意的(与高出价一起，它甚至提供了一个明确的标志来标识无效的出价): 投标人可以通过设置几个或高或低的无效出价来迷惑竞争对手。

