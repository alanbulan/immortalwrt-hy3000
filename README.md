<div align="center">

# HY3000 Firmware Workspace

HY3000 设备适配、固件构建配置与补丁工作区。

![Firmware](https://img.shields.io/badge/Domain-Firmware-818cf8?style=flat-square)
![Build](https://img.shields.io/badge/Build-GitHub_Actions-5eead4?style=flat-square)
![Scope](https://img.shields.io/badge/Scope-Device_Adaptation-fb7185?style=flat-square)

[源码导航](#源码导航) · [构建入口](#构建入口) · [验证与使用](#验证与使用)

</div>

本仓库保存 HY3000 相关配置、设备树、补丁和构建工作流，不是完整的 OpenWrt/ImmortalWrt 源码镜像。工作流会拉取指定上游，再应用本仓库的定制。

## 源码导航

| 入口 | 职责 |
| --- | --- |
| [.config](./.config) | 构建选项与软件包选择 |
| [diy-part1.sh](./diy-part1.sh) | feeds 处理前的定制步骤 |
| [diy-part2.sh](./diy-part2.sh) | 设备适配与构建定制 |
| [add-hy3000.sh](./add-hy3000.sh) | HY3000 适配脚本 |
| [patches](./patches) | 设备树、设备 profile、defconfig 与相关补丁 |
| [workflows](./.github/workflows) | 不同上游或构建目标的工作流 |

## 构建入口

| 工作流文件 | 用途入口 |
| --- | --- |
| [build.yml](./.github/workflows/build.yml) | ImmortalWrt HY3000 主构建入口 |
| [build-official.yml](./.github/workflows/build-official.yml) | 独立的官方源码构建配置，具体参数见文件 |
| [build-istoreos.yml](./.github/workflows/build-istoreos.yml) | iStoreOS 相关构建配置 |
| [build-padavanonly.yml](./.github/workflows/build-padavanonly.yml) | Padavan 相关构建配置 |
| [build-kmod-veth.yml](./.github/workflows/build-kmod-veth.yml) | veth 内核模块相关构建配置 |

主工作流 `build.yml` 使用手动触发，拉取 `immortalwrt/immortalwrt` 的 `openwrt-24.10` 分支，运行在 `ubuntu-22.04`，提供 `with_docker` 输入。其他工作流是不同入口，不应把一个入口的产物或设备参数套用到另一个入口。

```mermaid
flowchart LR
    Upstream[指定上游与分支] --> Source[下载源码与 feeds]
    Config[本仓库配置与补丁] --> Source
    Source --> Build[编译]
    Build --> Output[构建产物]
    Output --> Verify[设备 / 分区 / 校验和核对]
```

查阅 [Actions](https://github.com/alanbulan/immortalwrt-hy3000/actions) 的具体运行记录确认产物是否生成；不要由工作流文件的存在推断已经出包。固件编译会消耗较多构建时间和磁盘，本次没有触发构建。

## 验证与使用

仓库中的 `mt7981b-philips-hy3000*.dts`、设备 profile 和启动环境文件是适配证据，不是适用于所有同名硬件的刷机保证。使用任何产物前，需要核对实际硬件修订、芯片、闪存容量、分区布局、引导程序、镜像格式和恢复方法。

刷写之前先备份当前固件及必要配置，并准备可靠的恢复路径。`factory`、`sysupgrade`、内核模块和不同上游的产物不能互换使用；本文不提供未经本机验证的刷写命令。

上游及其软件包保留各自许可。构建通过、成功启动、网络与无线功能正常、长期运行稳定是不同的验收阶段，应分别记录；本仓库不因增加 README 就获得新的设备兼容或稳定性承诺。
