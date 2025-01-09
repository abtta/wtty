# Wtty 简介

[wtty](https://wtty.run) 是一款部署在 Linux 系统设备上的程序，目前支持 arm64、x86 架构。通过 wtty，用户可以在远程浏览器端（webtty）对设备进行伪终端（tty）控制操作。wtty 与 webtty 通过 WebRTC 技术实现实时通讯，而信令交换则可根据使用场景选择使用本地 WebSocket 或远程 MQTT 服务。

### 关系概览

- **wtty**：部署在设备上的后台程序。  
- **webtty**：部署在云端的前端页面（访问地址：[wtty.run/webtty/](https://wtty.run/webtty/)），用户通过浏览器访问该页面。
- **通信方式**：  
  - wtty 和 webtty 间的真正数据传输通过 WebRTC 点对点通道进行。
  - 在建立 WebRTC 通道前，需要信令交换。信令可通过两种方式：
    - 本地模式：使用 WebSocket 信令 (localws)
    - 远程模式：使用 MQTT 信令 (mqtt)

### 使用场景

- **本地 / 局域网环境**：推荐使用 localws 模式进行信令交换。这种模式适合在本地网络中快速访问。
- **远程 / 跨网络访问**：推荐使用 mqtt 模式进行信令交换。可在任意网络环境下通过公共或自建的 MQTT Broker 实现可靠的信令传递。


# 安装使用

本章节将介绍如何在 Linux 系统上安装并运行 wtty，以及通过命令行参数进行灵活配置，从而实现设备远程终端的 WebRTC 控制。

## 安装 wtty

wtty 是一款可直接在 Linux 环境（arm64、amd64）运行的可执行程序。您可通过官方提供的安装脚本快速安装。

**前置条件：**  
- 设备已安装 `curl`。  
- 设备具备网络访问权限。  
- 适用于 Linux 系统（arm64、amd64 架构）。

**在线安装：**  
```bash
curl -fsSL https://wtty.run/install.sh | bash
```
完成后，wtty 命令将被安装在系统的 $PATH 中，您可在终端中直接运行。

**使用说明：**  
wtty 可以通过命令行参数进行灵活配置，从而满足不同的应用场景（本地、远程、认证需求等）。
在运行前，请确保已满足以下条件：
- 若使用 `mqtt` 模式，请确认 MQTT Broker 可访问，并了解对应的 URL、用户名、密码（如有认证要求）。  
- 如需通过 webtty（浏览器端）对设备进行操作，请访问 wtty.run 在该页面中输入 wtty 生成的设备 ID 实现连接。  

**命令行参数说明** 

wtty 可通过以下参数进行配置：

| 参数名        | 默认值                          | 说明                                |
|---------------|---------------------------------|-------------------------------------|
| `-mode`       | `mqtt`                          | 启动模式，可选 `localws` 或 `mqtt`。 |
| `-url`        | `mqtt://broker.emqx.io:1883`    | MQTT Broker 的 URL，包括协议和端口。 |
| `-username`   | `null`                          | MQTT 用户名 (可选)。                 |
| `-password`   | `null`                          | MQTT 密码 (可选)。                   |
| `-ice`         | `stun:stun.l.google.com:19302` | ICE服务器，例: `turn:ip:3478,username,password` |
| `-id`         | *(未提供则自动生成)*            | 设备 ID，如未提供则随机生成。 |

## MQTT模式（`mqtt`）

在 `mqtt` 模式下，下述命令会在本地启动 wtty，并输出一个设备 ID，例如：wttyf1e2d3c4ab...。请记录该 ID。

**示例：**

```bash
wtty
```

## 本地 WebSocket 模式（`localws`）

在 `localws` 模式下，程序会启动本地 WebSocket 服务。此模式不需要外部 MQTT 服务，即可实现信令交换和 WebRTC 通道建立。

**示例：**

```bash
wtty -mode=localws
```
