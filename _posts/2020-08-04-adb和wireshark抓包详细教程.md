---
layout: post
title:  "adb和wireshark抓包详细教程"
author: "testomg"
comments: false
tags: 测试工具
---


## 第一章 简介

常用的抓包方式有两种，一种是通过ADB工具（针对安卓设备），一种是通过Wireshark抓包。

ADB（Android 调试桥）是一种功能多样的命令行工具，可让您与设备进行通信。adb 命令可用于执行各种设备操作（例如安装和抓取日志）。

- 特点：电脑和目标设备在同一个网络下就能使用。

Wireshark（前称Ethereal）是一个网络封包分析软件。通过监听网卡通信抓取数据包。

- 使用hub（集线器）连接电脑和目标设备。

## 第二章 如何使用adb抓包

### 2.1 安装


1. 解压adb.zip文件到任意目录（例如E:\tools\）。

2. 运行adb.bat，在窗口中输入adb.exe，出现如下内容表示能够正常运行adb工具：
![image](https://note.youdao.com/yws/api/personal/file/2457061639324980BD7FA887C71B8169?method=download&shareKey=4d25713d0d8db6caabb125532fbe2f25)

### 2.2 通过adb连接目标设备

1. 确保电脑和目标设备链接到同一个网络中（例如同一个WIFI）。
2. 开启目标设备的USB调试模式：
    - 进入设置页面。
    - 找到系统版本的位置，双击确定键，直到页面出现“进入开发者模式”。
    - 此时设置页面会出现“开发者选项”之类的入口，进入后，找到“USB调试”并开启。
3. 查看目标设备IP。
4. 在电脑中打开adb.bat文件，执行以下命令（connect是连接，devices是查看连接状态），如果第二条命令执行后ip右侧显示devices则表示连接成功。

```python
adb.exe connect 192.168.1.100
adb.exe devices
```

![image](https://note.youdao.com/yws/api/personal/file/181050C2D5A14F31A06C8F439BDEF19D?method=download&shareKey=cba6d443dd8db3a2a79b043ba55565e1)

### 2.3 使用adb抓包并保存日志

1. 执行以下抓包命令（后面的文件名称可以任意更改）。

```python
adb.exe logcat -v time >>E:\tools\adb\log.txt
```
![image](https://note.youdao.com/yws/api/personal/file/A58CF7B4ED7248DFB652D11694DA4738?method=download&shareKey=8d9e7c1fad1b413cec623497e6937d35)

2. 此时已经进入抓包状态，目标设备的所有日志记录都会保存。
3. 在黑色命令窗口中按“Ctrl+C”退出抓包程序，此时打开前面设置的保存路径，会看到文件“log.txt”。完成。

### 2.4 异常情况处理


#### 连接目标设备时，提示：unable to connect

解决方法：

- 确保目标设备是否已经开启开发者模式和usb调试模式。
- 检查电脑和目标设备是否是同一个网络。
- 检查网络是否异常。

#### 查看连接状态，不是device

解决方法：

- 重启目标设备，再重试。
- 检查是否有其他电脑工具已经连接了目标设备。

#### 使用logcat抓包时，提示：waiting for device

解决方法：

- 可能链接了多台设备或者工具异常，尝试执行如下命令后再连接:

```python
adb.exe kill-server
adb.exe start-server
```

## 第三章 Wireshark工具抓包流程

### 3.1 需要的物理设备

- 集线器（有镜像口或可抓包的）：
    - https://item.taobao.com/item.htm?spm=a1z0k.7386009.0.d4919233.1d7171ceY518dW&id=542398510763&_u=t2dmg8j26111
- 三条网线（其中一条是连接上网的网线）

### 3.2 安装wireshark和连接设备

1. 双击Wireshark-win64-3.2.2.exe文件安装wireshark。
2. 一直选择下一步直到安装完成。
3. 在开始菜单中找到wireshark，打开后是如下界面：

![image](https://note.youdao.com/yws/api/personal/file/BD5BC7DBD8FF4448993D8977F896E7A9?method=download&shareKey=4b5b5fea3a6bba341fb2b6fcf1d96a7b)

4. （该步骤的hub为3.1节中的设备，其他设备请根据具体插口情况进行连接）将可以上网的网线接入hub第一个插口中，用一条网线将hub第二个插口和电脑连接，再用一条网线将hub（3-5口）和目标设备连接。参考如下拓扑图：

![image](https://note.youdao.com/yws/api/personal/file/166DCCA2AE574C29A029121846EEFFF5?method=download&shareKey=95457a444680407b8de26cb849dabf38)

### 3.3 查看网线连接的是哪个网卡

1. 右键右下角的网络，选择“打开网络和Internet设置”。
2. 选择“网络和共享中心”
3. 在左侧点击“更改适配器设置”
4. 此时可能会看到多个网卡图标，尝试拔掉网线和插入网线，看看哪个图标发生变化（未接网线时图标有红叉），记住有变化图标的名称。例如下图有变化的是“以太网”

### 3.4 使用wireshark抓包并保存日志

1. 打开wireshark。
2. 双击网线连接的网卡名称。例如下图选择“以太网”：
![image](https://note.youdao.com/yws/api/personal/file/844F24F0D4434CC9818DF428BE81A8FA?method=download&shareKey=2a5a454e6d32051844c5e7450be674cd)
3. 此时wireshark会自动开始捕捉以太网网卡的通信信息。
![image](https://note.youdao.com/yws/api/personal/file/503DB65527AB465F93DD9C42C3A539A5?method=download&shareKey=644aa46bf005631b19f453fd815f9c4e)
4. 获取足够的信息后，点击左上角的红色按钮，停止捕获。
![image](https://note.youdao.com/yws/api/personal/file/0CA1E10F420F458E831A7DA186CFC86C?method=download&shareKey=7c88644dc0cec851912d053d816f1eb4)
5. 点击文件-另存为，保存日志。完成。
