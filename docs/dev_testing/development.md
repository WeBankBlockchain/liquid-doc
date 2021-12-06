# 开发指南

本节将以[HelloWorld 合约](../quickstart/example.html#hello-world)为例介绍 Liquid 智能合约的开发步骤，将会涵盖智能合约的创建、测试、构建、部署及调用等步骤。

## 创建

在终端中执行以下命令创建 Liquid 智能合约项目：

```shell
cargo liquid new contract hello_world
```

```eval_rst
.. note::

   ``cargo liquid`` 是调用命令行工具 ``cargo-liquid`` 的另一种写法，这种写法使得 ``liquid`` 看上去似乎是 ``cargo`` 的子命令。
```

上述命令将会在当前目录下创建一个名为 hello_world 的智能合约项目，此时会观察到当前目录下新建了一个名为“hello_world”的目录：

```shell
cd ./hello_world
```

hello_world 目录内的文件结构如下所示：

```
hello_world/
├── .gitignore
├── .liquid
│   └── abi_gen
│       ├── Cargo.toml
│       └── main.rs
├── Cargo.toml
└── src
│   └──lib.rs
```

其中各文件的功能如下：

-   `.gitignore`：隐藏文件，用于告诉版本管理软件[Git](https://git-scm.com/)哪些文件或目录不需要被添加到版本管理中。Liquid 会默认将某些不重要的问题件（如编译过程中生成的临时文件）排除在版本管理之外，如果不需要使用 Git 管理对项目版本进行管理，可以忽略该文件；

-   `.liquid/`：隐藏目录，用于实现 Liquid 智能合的内部功能，其中`abi_gen`子目录下包含了 ABI 生成器的实现，该目录下的编译配置及代码逻辑是固定的，如果被修改可能会造成无法正常生成 ABI；

-   `Cargo.toml`：项目配置清单，主要包括项目信息、外部库依赖、编译配置等，一般而言无需修改该文件，除非有特殊的需求（如引用额外的第三方库、调整优化等级等）；

-   `src/lib.rs`：Liquid 智能合约项目根文件，合约代码存放于此文件中。智能合约项目创建完毕后，`lib.rs`文件中会自动填充部分样板代码，我们可以基于这些样板代码做进一步的开发。

我们将[HelloWorld 合约](../quickstart/example.html#hello-world)中的代码复制至`lib.rs`文件中后，便可进行后续步骤。

## 测试

在正式部署之前，在本地对智能合约进行详尽的单元测试是一种良好的开发习惯。Liquid 内置了对区块链链上环境的模拟，因此即使不将智能合约部署上链，也能够在本地方便地执行单元测试。在 hello_world 项目根目录下执行以下命令即可执行我们预先编写好的单元测试用例：

```bash
cargo test
```

```eval_rst
.. admonition:: 注意

   上述命令与创建合约项目时的命令有所不同：

   #. 命令中并不包含 ``liquid`` 子命令，因为Liquid可以使用标准cargo单元测试框架来执行单元测试，因此并不需要调用 ``cargo-liquid`` 。
```

命令执行结束后，显示如下内容：

```bash
running 2 tests
test hello_world::tests::get_works ... ok
test hello_world::tests::set_works ... ok

test result: ok. 2 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s

   Doc-tests hello_world

running 0 tests

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s
```

从结果中可以看出，所有用例均通过了测试，因此可以有信心认为智能合约中的逻辑实现是正确无误的 😄。我们接下来将开始着手构建 HelloWorld 智能合约，并把它部署至真正的区块链上。

## 构建

在 hello_world 项目根目录下执行以下命令即可开始进行构建：

```bash
cargo liquid build
```

该命令会引导 Rust 语言编译器以`wasm32-unknown-unknown`为目标对智能合约代码进行编译，最终生成 Wasm 格式字节码及 ABI。命令执行完成后，会显示如下形式的内容：

```
:-) Done in 9 seconds, your project is ready now:
Binary: C:/Users/liche/hello_world/target/hello_world.wasm
ABI: C:/Users/liche/hello_world/target/hello_world.abi
```

其中，“Binary:”后为生成的字节码文件的绝对路径，“ABI:”后为生成的 ABI 文件的绝对路径。为尽量简化 FISCO BCOS 各语言 SDK 的适配工作，Liquid 采用了与 Solidity ABI 规范兼容的 ABI 格式，HelloWorld 智能合约的 ABI 文件内容如下所示：

```json
[
    {
        "inputs": [],
        "type": "constructor"
    },
    {
        "constant": true,
        "inputs": [],
        "name": "get",
        "outputs": [
            {
                "internalType": "string",
                "type": "string"
            }
        ],
        "type": "function"
    },
    {
        "conflictFields": [
            {
                "kind": 0,
                "path": [],
                "read_only": false,
                "slot": 0
            }
        ],
        "constant": false,
        "inputs": [
            {
                "internalType": "string",
                "name": "name",
                "type": "string"
            }
        ],
        "name": "set",
        "outputs": [],
        "type": "function"
    }
]
```

```eval_rst

.. hint::

   构建过程中会从GitHub拉取Liquid的相关依赖包，若无法正常访问GitHub，则请在项目中将 ``git = "https://github.com/WeBankBlockchain/liquid"`` **全局** 替换为 ``git = "https://gitee.com/WeBankBlockchain/liquid"`` 。
```

```eval_rst
.. hint::

   如果希望构建出能够在国密版FISCO BCOS区块链底层平台上运行的智能合约，请在执行构建命令时添加-g选项，例如： ``cargo liquid build -g``。
```

## 部署

### 搭建 FISCO BCOS 区块链

// TO-DO 3.0文档
当前，FISCO BCOS 对 Wasm 虚拟机的支持尚未合入主干版本，仅开放了实验版本的源代码及可执行二进制文件供开发者体验，因此需要按照以下步骤手动搭建 FISCO BCOS 区块链：

1. 根据[依赖项说明](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/installation.html#id2)中的要求安装依赖项；

2. 下载实验版本的建链工具 build_chain.sh：

    ```shell
    cd ~ && mkdir -p fisco && cd fisco
    curl -#LO https://github.com/WeBankBlockchain/liquid/releases/download/v1.0.0-rc1/build_chain.sh && chmod u+x build_chain.sh
    ```

    ```eval_rst

    .. hint::

       若无法访问GitHub，则请执行 ``curl -#LO https://gitee.com/WeBankBlockchain/liquid/attach_files/651253/download/build_chain.sh`` 命令下载 build_chain.sh。
    ```

3. 使用 build_chain.sh 在本地搭建一条单群组 4 节点的 FISCO BCOS 区块链并运行。更多 build_chain.sh 的使用方法可参考其[使用文档](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/manual/build_chain.html)：
    
    ```shell
    bash build_chain.sh -l 127.0.0.1:4 -p 30300,20200,8545
    bash nodes/127.0.0.1/start_all.sh
    ```

### 部署 Java SDK

// TO-CHECK
Java SDK提供了访问FISCO BCOS 节点的Java API，支持节点状态查询、部署和调用合约等功能，基于Java SDK可开发区块链应用，目前支持FISCO BCOS 2.0+。Java SDK部署方式可参考其[官方文档](https://gitee.com/FISCO-BCOS/java-sdk)。

```eval_rst
.. code-block:: shell
   :linenos:
   :emphasize-lines: 2,4

   git clone -b release-3.1.0 https://github.com/FISCO-BCOS/console.git
   cd console && ./gradlew build
   cd dist
   cp -n conf/config-example.toml conf/config.toml
   #配置SDK证书，将SDK证书拷贝到Java SDK的示例如下(这里假设nodes和console均在fisco目录下)
   cp -r ../../nodes/127.0.0.1/sdk/* conf/
```

```eval_rst

.. hint::

   若无法访问GitHub，则请执行 ``git clone https://gitee.com/FISCO-BCOS/console`` 命令克隆java console。
```

### 将合约部署至区块链

使用 Node.js SDK CLI 工具提供的`deploy`子命令，我们可以将 Hello World 合约构建生成的 Wasm 格式字节码部署至真实的区块链上，`deploy`子命令的使用说明如下：

```
cli.js exec deploy <contract> [parameters..]

Deploy a contract written in Solidity or Liquid

Positionals:
  contract    The path of the contract                       [string] [required]
  parameters  The parameters(split by space) of constructor
                                                           [array] [default: []]

Options:
  --version   Show version number                                      [boolean]
  --abi, -a   The path of the corresponding ABI file                    [string]
  --who, -w   Who will do this operation                                [string]
  -h, --help  Show help                                                [boolean]
```

执行该命令时需要传入字节码文件的路径及构造函数的参数，并通过`--abi`选项传入 ABI 文件的路径。当根据配置手册(https://gitee.com/FISCO-BCOS/nodejs-sdk#22-%E9%85%8D%E7%BD%AE)配置好 CLI 工具后，可以使用以下命令部署 HelloWorld 智能合约。由于合约中的构造函数不接受任何参数，因此无需在部署时提供参数：

```shell
node ./cli.js exec deploy C:/Users/liche/hello_world/target/hello_world.wasm --abi C:/Users/liche/hello_world/target/hello_world.abi
```

部署成功后，返回如下形式的结果，其中包含状态码、合约地址及交易哈希：

```json
{
    "status": "0x0",
    "contractAddress": "0x039ced1cd5bea5ace04de8e74c66e312ba4a48af",
    "transactionHash": "0xf84811a5c7a5d3a4452a65e6929a49e69d9a55a0f03b5a03a3e8956f80e9ff41"
}
```

## 调用

使用 Node.js SDK CLI 工具提供的`call`子命令，我们可以调用已被部署到链上的智能合约，`call`子命令的使用方式如下：

```
cli.js exec call <contractName> <contractAddress> <function> [parameters..]

Call a contract by a function and parameters

Positionals:
  contractName     The name of a contract                    [string] [required]
  contractAddress  20 Bytes - The Address of a contract      [string] [required]
  function         The function of a contract                [string] [required]
  parameters       The parameters(split by space) of a function
                                                           [array] [default: []]

Options:
  --version   Show version number                                      [boolean]
  --who, -w   Who will do this operation                                [string]
  -h, --help  Show help                                                [boolean]
```

执行该命令时需要传入合约名、合约地址、要调用的合约方法名及传递给该合约方法的参数。以调用 HelloWorld 智能合约中的`get`方法为例，可以使用以下命令调用该方法。由于`get`方法不接受任何参数，因此无需在调用时提供参数：

```bash
node .\cli.js exec call hello_world 0x039ced1cd5bea5ace04de8e74c66e312ba4a48af get
```

调用成功后，返回如下形式结果：

```json
{
    "status": "0x0",
    "output": {
        "function": "get()",
        "result": ["Alice"]
    }
}
```

其中`output.result`字段中包含了`get`方法的返回值。可以看到，`get`方法返回了字符串“Alice”。
