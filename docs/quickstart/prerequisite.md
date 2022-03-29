# 环境配置

```eval_rst
.. admonition:: 注意

   受限于网络情况及机器性能，本小节中部分依赖项的安装过程可能较为耗时，请耐心等待。必要时可能需要配置网络代理。

   推荐切换Rustup官方镜像源为国内镜像源，请参考 `Rustup 镜像安装帮助 <https://mirrors.tuna.tsinghua.edu.cn/help/rustup/>`_ 更换镜像源。
```


## 部署 Rust 编译环境

Liquid 智能合约的构建过程主要依赖 Rust 语言编译器`rustc`及代码组织管理工具`cargo`，且均要求版本号大于或等与 1.50.0。如果此前从未安装过`rustc`及`cargo`，可参考下列步骤进行安装：

- 对于 Mac 或 Linux 用户，请在终端中执行以下命令；

    ```shell
    # 此命令将会自动安装 rustup，rustup 会自动安装 rustc 及 cargo
    curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
    ```

- 对于 32 位 Windows 用户，请从[此处](https://static.rust-lang.org/rustup/dist/i686-pc-windows-msvc/rustup-init.exe)下载安装 32 位版本安装程序。

- 对于 64 位 Windows 用户，请从[此处](https://static.rust-lang.org/rustup/dist/x86_64-pc-windows-msvc/rustup-init.exe)下载安装 64 位版本安装程序。

如果此前安装过`rustc`及`cargo`，但是未能最低版本要求，则可在终端中执行以下命令进行更新：

```shell
rustup update
```

安装完毕后，分别执行以下命令验证已安装正确版本的 `rustc` 及 `cargo`：

```shell
rustc --version
cargo --version
```

此外需要安装以下工具链组件：

```shell
rustup toolchain install nightly-2021-06-23 --component rust-src rustc-dev llvm-tools-preview
rustup default nightly-2021-06-23
rustup target add wasm32-unknown-unknown
```

```eval_rst
.. admonition:: 注意

   由于Liquid使用了少量目前尚不稳定的Rust语言特性，因此在构建时需要依赖 ``nightly`` 版本的 ``rustc`` 。但是这些特性目前已经被广泛应用在Rust项目中，因此其可靠性值得信赖。随着Rust语言迭代演进，这些特性终将变为稳定特性。
```

```eval_rst
.. admonition:: 注意

   所有可执行程序都会被安装于 ``$HOME/cargo/bin`` 目录下，包括 ``rustc`` 、 ``cargo`` 及 ``rustup`` 等。为方便使用，需要将 ``$HOME/cargo/bin`` 目录加入到操作系统的 ``PATH`` 环境变量中。在安装过程中， ``rustup`` 会尝试自动配置 ``PATH`` 环境变量，但是由于权限等原因，该过程可能会失败。当发现 ``rustc`` 或 ``cargo`` 无法正常执行时，可能需要手动配置该环境变量。
```

```eval_rst
.. admonition:: 注意

   如果当前网络无法访问Rustup官方镜像，请参考 `Rustup 镜像安装帮助 <https://mirrors.tuna.tsinghua.edu.cn/help/rustup/>`_ 更换镜像源。
```

构建 Liquid 智能合约的过程中需要下载大量第三方依赖，若当前网络无法正常访问 crates.io 官方镜像源，则按照以下步骤为 `cargo` 更换镜像源：

```shell
# 编辑cargo配置文件，若没有则新建
vim $HOME/.cargo/config
```

并在配置文件中添加以下内容：

```ini
[source.crates-io]
registry = "https://github.com/rust-lang/crates.io-index"
replace-with = 'ustc'
[source.ustc]
registry = "git://mirrors.ustc.edu.cn/crates.io-index"
```

## 安装其他依赖

请确保配置 ``cmake`` 环境，Linux可以通过以下命令安装：

```shell
# Ubuntu请执行下面的命令
sudo apt install cmak
# CentOS请执行下面的命令
sudo yum install cmake3
```

Mac下可以直接通过 ``homebrew`` 安装：

```shell
brew install cmake
```

如果CentOS的yum资源无cmake3（如CentOS 7），则需要手动下载cmake3进行配置

以下载`cmake 3.21.3`版本为例，到[cmake官网](https://cmake.org/download/)下载包后，解压到`/data/home/software`目录，得到了`cmake-3.21.3-linux-x86_64`目录，并修改`/etc/profile`以设置cmake环境变量

```bash
vi /etc/profile
export CMAKE3_HOME=//data/home/software/cmake-3.21.3-linux-x86_64
export PATH=$PATH:$CMAKE3_HOME/bin

# 环境变量生效
source /etc/profile
```


## 安装 cargo-liquid

`cargo-liquid` 是用于辅助开发 Liquid 智能合约的命令行工具，在终端中执行以下命令安装：

```shell
cargo install --git https://github.com/WeBankBlockchain/cargo-liquid --tag v1.0.0-rc2 --force
```

```eval_rst
.. admonition:: 注意

   若无法正常访问GitHub，则请执行 ``cargo install --git https://gitee.com/WeBankBlockchain/cargo-liquid --tag v1.0.0-rc2 --force`` 命令进行安装。
```

如果使用的是CentOS系统，安装前，确保按此[Issue检查依赖](https://github.com/WeBankBlockchain/cargo-liquid/issues/14)
- 确保cmake版本大于3.12
- 安装devtoolset并启用
- 安装rust工具链
```bash
#请确保cmake版本大于3.12
#请参考下述命令使用gcc7
sudo yum install -y epel-release centos-release-scl
sudo yum install -y devtoolset-7
source /opt/rh/devtoolset-7/enable
#请参考下述命令使用要求版本的rust工具链
rustup toolchain install nightly-2021-06-23 --component rust-src rustc-dev llvm-tools-preview
rustup default nightly-2021-06-23
```
确保上述工具版本符合要求后，再次重新执行cargo install命令安装

开始安装后，以gitee为例，会输出类似的日志：
```
Updating git repository `https://gitee.com/WeBankBlockchain/cargo-liquid`
  Installing cargo-liquid v1.0.0-rc2 (https://gitee.com/WeBankBlockchain/cargo-liquid?tag=v1.0.0-rc2#5da4da65)
Updating `git://mirrors.ustc.edu.cn/crates.io-index` index
  Fetch [=======>                 ]  34.20%, 5.92MiB/s
```
如果下载crates包失败，可重新执行cargo install命令重试下载

安装成功后，会输出如下日志：
```
Compiling wabt v0.10.0
    Finished release [optimized] target(s) in 1m 33s
  Installing /data/home/webase/.cargo/bin/cargo-liquid
  Installing /data/home/webase/.cargo/bin/liquid-analy
   Installed package `cargo-liquid v1.0.0-rc2 (https://gitee.com/WeBankBlockchain/cargo-liquid?tag=v1.0.0-rc2#5da4da65)` (executables `cargo-liquid`, `liquid-analy`)
```

## 推荐安装 Binaryen（可选）

推荐安装Binaryen以优化编译过程

Binaryen 项目中包含了一系列 Wasm 字节码分析及优化工具，其中如 `wasm-opt` 等工具会在 Liquid 智能合约的构建过程中使用。请参考其[官方文档](https://github.com/WebAssembly/binaryen#building)。

除根据官方文档的编译安装方式外
- Ubuntu下可通过 ``sudo apt install binaryen`` 下载安装（如使用Ubuntu，则系统版本不低于20.04
- Mac下可直接通过 ``brew install binaryen`` 下载安装binaryen
- 其他操作系统可参照[此处](https://pkgs.org/download/binaryen)查看是否可直接通过包管理工具安装）
  - 如CentOS则参考
  ```bash
  # 下载binaryen的rpm包
  wget https://download-ib01.fedoraproject.org/pub/epel/7/x86_64/Packages/b/binaryen-104-1.el7.x86_64.rpm
  # 安装rpm包
  sudo rpm -ivh binaryen-104-1.el7.x86_64.rpm
  ```