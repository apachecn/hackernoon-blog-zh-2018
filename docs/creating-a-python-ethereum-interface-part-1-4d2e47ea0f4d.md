# 创建 Python 以太坊接口:第 1 部分

> 原文：<https://medium.com/hackernoon/creating-a-python-ethereum-interface-part-1-4d2e47ea0f4d>

![](img/98e5e81abaad6c1ba4db9ced1afccb6c.png)

Photo by [Leonel Fernandez](https://unsplash.com/photos/REZp_5-2wzA?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/buttons?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

来自 Python 背景的我最初有点沮丧地发现缺少使用 Python 与以太坊区块链交互的教程。然而，在阅读了>>> 0xf52cef744ccdd52e66856057d820d2b6677af63c #account 0 from above

*俏皮。*

在下一行中，设置存放合同的 path 变量，如果您只是使用包含的合同，将如下所示:

```
contract_dir = os.path.abspath('./contracts/')
```

最后，您需要创建一个接口实例，包括:

```
greeter_interface = ContractInterface(w3, 'Greeter', contract_dir)
```

这里是你希望为其创建界面的合同的名称。因为 Greeter.sol 继承自 Owned.sol，所以您可以从 Greeter 接口访问 Owned 中的所有方法。

让我们用下面的代码简单地运行一下`walkthrough.py`,以确保我们在初始化时不会出现任何错误:

```
python3 -i walkthrough.py
>>> type(greeter_interface)
```

应返回`<class 'interface.ContractInterface'>`

此外，如果您需要任何接口方法的快速参考，您可以运行`>>> help(ContractInterface)`获得 docstring 输出。

# 编制

Solc 对于编译没有显式传递的依赖项非常严格(这是正确的)。使用 Truffle 时，一切都编译得很好，但是 Solc 做了一些修改。如果您正在导入合同，请确保在您的`pragma`下方使用这个特定的格式`import "./contract.sol";`。编译器应该负责剩下的工作。继续将以下内容添加到`walkthrough.py`:

`greeter_interface.compile_source_files()`

此时，`walkthrough`应该是什么样子

```
# Put your imports here
import os
from interface import ContractInterface
from web3 import HTTPProvider, Web3# Initialize your web3 object
w3 = Web3(HTTPProvider('[http://127.0.0.1:8545'](http://127.0.0.1:8545')))# Create a path object to your Solidity source files
contract_dir = os.path.abspath('./contracts/')# Initialize your interface
greeter_interface = ContractInterface(w3, 'Greeter', contract_dir)# Compile contracts below
greeter_interface.compile_source_files()
```

让我们更详细地看看`compile_source_files()`方法:

```
deployment_list = []for contract in os.listdir(self.contract_directory):
    deployment_list.append(
        os.path.join(self.contract_directory,  contract)
    )self.all_compiled_contracts = compile_files(deployment_list)print('Compiled contract keys:\n{}'.format(
        '\n'.join(self.all_compiled_contracts.keys())
    ))
```

这个方法只是创建了一个要传递给 py-solc 的`compile_files`的绝对路径列表。然后，编译器输出被保存到实例属性中，供以后使用。如果您现在运行`walkthrough`，您应该会看到编译后的合同密钥打印出来。

# 部署

让我们用以下代码行将部署方法添加到`walkthrough`:

```
greeter_interface.deploy_contract()
```

此方法首先检查协定是否已编译，如果没有，则使用以下代码重新编译它们:

```
try:
    self.all_compiled_contracts is not None
except AttributeError:
    print("Source files not compiled, compiling now and trying again...")
    self.compile_source_files()
```

接下来，它将在一个编译器输出键中找到我们之前指定要部署的契约的名称。然后，它使用该契约的应用程序二进制接口(ABI)和字节码(BIN)创建一个部署实例:

```
for compiled_contract_key in self.all_compiled_contracts.keys():
    if self.contract_to_deploy in compiled_contract_key:
        deployment_compiled = self.all_compiled_contracts[
            compiled_contract_key
        ]deployment = self.web3.eth.contract(
    abi=deployment_compiled['abi'],
    bytecode=deployment_compiled['bin']
    )
```

这是我们第一次看到 web3.py 库的运行。web3.eth.contract 是一个准备在区块链上部署的协定类。接下来，我们将估计部署的气体使用量，如果低于初始化时设置的值，就进行部署。

```
deployment_estimate = deployment.constructor().estimateGas(
    transaction=deployment_params)if deployment_estimate < self.max_deploy_gas:
    tx_hash = deployment.constructor().transact(
        transaction=deployment_params)
```

这里发生了一些事情。`constructor()`方法构建给定`[deployment_params](http://deployment_estimate = deployment.constructor().estimateGas(transaction=deployment_params)                  if deployment_estimate < self.max_deploy_gas:                     tx_hash = deployment.constructor().transact(transaction=deployment_params))`的部署事务。这是一个有许多缺省值的字典，如果需要可以重载。`max_deploy_gas`在`__init__`期间被设置为默认值，实际上只是一个意外部署气体使用的安全特性。如果通过，使用`constructor().transact`部署契约，并返回事务散列(`tx_hash`)。

```
tx_receipt = self.web3.eth.waitForTransactionReceipt(tx_hash)
contract_address = tx_receipt['contractAddress']
```

这两行等待事务被挖掘，返回收据，并从中提取合同的地址。我们将使用以下内容将其写入文件:

```
vars = {
    'contract_address' : contract_address,
    'contract_abi' : deployment_compiled['abi']
}with open (self.deployment_vars_path, 'w') as write_file:
    json.dump(vars, write_file, indent=4)
```

这将收集部署地址和契约的 ABI，并将它们保存到位于`deployment_vars_path`中指定路径的 JSON 文件中。我们将在下一节看到原因。

现在，如果您运行`walkthrough`，您应该会看到编译合同的输出，一些合同部署输出和一行告诉您那些变量保存在哪里的内容。

# 获取实例

一旦我们将合同部署到区块链，它将无限期地存在下去(至少在理论上是这样)。我们不希望每次想要与它交互时都要重新编译和重新部署。这就是为什么我们在最后一节将合同的地址和 ABI 保存在 JSON 文件中。将以下内容添加到`walkthrough`:

```
greeter_interface.get_instance()
```

这个方法的第一部分将打开 JSON，检查它是否有一个地址，然后检查在那个地址上是否部署了一些东西:

```
with open (self.deployment_vars_path, 'r') as read_file:
    vars = json.load(read_file)try:
    self.contract_address = vars['contract_address']
except ValueError(
    "No address found in {}, please call 'deploy_contract' and 
    try again.".format(self.deployment_vars_path)
    ):
    raisecontract_bytecode_length = len(self.web3.eth.getCode(
     self.contract_address).hex())try:
    assert (contract_bytecode_length > 4), "Contract not deployed 
    at {}.".format(self.contract_address)
except AssertionError as e:
    print(e)
    raise
else:
    print('Contract deployed at {}. This function returns 
          an instance object.'.format(self.contract_address))
```

一旦这些检查完成，我们将再次构建契约实例，这次使用地址并返回该实例。

```
self.contract_instance = self.web3.eth.contract(
     abi = vars['contract_abi'],
     address = vars['contract_address']
     )return self.contract_instance
```

这个部署的实例公开了您的契约的属性和方法，如[文档](http://self.contract_instance = self.web3.eth.contract(             abi = vars['contract_abi'],             address = vars['contract_address']         )          return self.contract_instance)中所述。举个例子，

```
>>>instance = greeter_interface.get_instance()
>>>instance.functions.greet().call()
>>>b'Hello'\x00\x00\x00\x00...
```

问候语这样显示是因为它在`Greeter.sol`中被设置为类型`bytes32`。我们将在本教程的第 2 部分中探讨我为什么使用字节，以及处理事件和清理输出！然而，对于许多人来说，这个契约实例足以满足他们的需求。

# 包裹

我编写这个小接口是因为我想要一种简洁的方式来编译、部署 Python 中的智能契约并与之交互。这对我来说是一次很大的学习经历，也是大量修补的结果——我希望它对你们中的一些人有所帮助。然而，它相当固执己见，当然也不像我希望的那样健壮。这是一项正在进行的工作，我乐于接受批评和改进。同样，再次，如果你需要任何帮助，留下评论，我会尽力而为！