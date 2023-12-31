# 使用VMware虚拟机安装openEuler22/23

> 2023年9月10日 Kai 未经授权，谢绝转载

子教程介绍了如何在VMware Workstudio上新建到安装一个虚拟机	这个教程用到的是openEuler系统，在主教程中介绍了openEuler镜像的下载

[返回主教程](./readme.md)

## 1. 新建虚拟机

![image-20230910223232234](./assets/image-20230910223232234.png)

1.2 选择自定义，然后下一步

![image-20230910223343479](./assets/image-20230910223343479.png)

1.3 设置兼容性（根据自己的需要设置），然后下一步

![image-20230910223458582](./assets/image-20230910223458582.png)

1.4 选择稍后安装操作系统，然后下一步

![image-20230910223529283](./assets/image-20230910223529283.png)



1.5 选择Linux系统，64位，然后下一步

![image-20230910223628937](./assets/image-20230910223628937.png)

1.6 自行设置虚拟机名称，然后下一步

![image-20230910223659196](./assets/image-20230910223659196.png)

1.7 根据自己电脑的性能和所要虚拟机的性能设置处理器数量等，然后下一步

*数量越多，虚拟机越流畅，随之虚拟机占用的资源也要多，主机可能就会卡顿*

![image-20230910223755054](./assets/image-20230910223755054.png)

1.8 同1.7，根据自己的情况设置虚拟机内存，建议不要低于1G，然后下一步

![image-20230910223951711](./assets/image-20230910223951711.png)

1.9 设置网络为网络转发（NAT），然后下一步

*其他两种也可以使用*

![image-20230910224100198](./assets/image-20230910224100198.png)

1.10 使用磁盘推荐类型SCSI，下一步

![image-20230910224241761](./assets/image-20230910224241761.png)

1.11 创建新的虚拟磁盘，下一步

*如果你有要利用的磁盘，可以选择旧磁盘*

![image-20230910224344068](./assets/image-20230910224344068.png)

1.12 设置磁盘容量，建议不要小于8G，后期储存数据也需要空间，其他选项界面中有说明，然后下一步

![image-20230910224451000](./assets/image-20230910224451000.png)

1.13 设置磁盘名称，一般不修改，然后下一步

![image-20230910224549756](./assets/image-20230910224549756.png)

1.14 完成虚拟机创建，在这里你可以再检查一下配置，点击”完成“

![image-20230910224630430](./assets/image-20230910224630430.png)

## 2. 安装openEuler

2.1 设置启动项

”编辑虚拟机设置“->选择”CD/DVD“->选择”使用 ISO 映像文件“->然后使用下载好的映像（ISO后缀的文件），然后确定

![image-20230910225010159](./assets/image-20230910225010159.png)

2.2 安装openEuler

启动虚拟机（可点击“开启此虚拟机”），然后选择想要的安装流程。如果在第一种出现问题，可以选择用第二种更详细的查看问题

![image-20230910225633930](./assets/image-20230910225633930.png)

2.3 配置openEuler

等待较长时间后会出现安装界面，

2.3.1 选择语言，根据自己的需要选择语言，然后”继续“（”continue“）

![image-20230910225753319](./assets/image-20230910225753319.png)

2.3.2 设置存储

**方案1**

选择系统下的”安装目的地“

![image-20230910225832381](./assets/image-20230910225832381.png)

选择虚拟磁盘

![image-20230910225917320](./assets/image-20230910225917320.png)

选择”完成“

**方案2**

这里拿20版本的示例

在上方选择该磁盘后选择“自定义”

![image-20230910230121146](./assets/image-20230910230121146.png)

点击“完成”后跳转到 手动分区 界面

![image-20230910230306527](./assets/image-20230910230306527.png)

”点击这里自动创建它们“，然后点击”完成“

![image-20230910230352794](./assets/image-20230910230352794.png)

在弹出的界面选择”接受更改“

![image-20230910230445724](./assets/image-20230910230445724.png)

2.3.3 设置网络

选择 系统 下的 网络和主机名

![image-20230910230605614](./assets/image-20230910230605614.png)

将网络打开

![image-20230910230642640](./assets/image-20230910230642640.png)

2.3.4 选择软件

选择 软件 下的 软件选择

![image-20230910230736173](./assets/image-20230910230736173.png)

选择自己需要的程序，主教程附带openGauss的安装，这里顺带选上（这是openEuler22/23版本）

![image-20230910230852715](./assets/image-20230910230852715.png)

点击完成

2.3.5 设置账户

设置root账户（推荐）

![image-20230910230938303](./assets/image-20230910230938303.png)

启用root账户，并设置密码（三种类型，长度8位及以上）

![image-20230910231030740](./assets/image-20230910231030740.png)

然后”完成“

2.4 开始安装

点击”开始安装“，开始安装

![image-20230910231107245](./assets/image-20230910231107245.png)

2.5 取消安装程序（或者”我已完成安装“）

安装完成后重启系统

![image-20230910231240726](./assets/image-20230910231240726.png)

然后强制关闭系统

![image-20230910231356975](./assets/image-20230910231356975.png)

取消映像启动

![image-20230910231444877](./assets/image-20230910231444877.png)

## 3. 进入系统

重新启动虚拟机，之后会进入操作界面![image-20230910231650070](./assets/image-20230910231650070.png)

你已经成功安装了openEuler

[返回主教程](./readme.md)

************************

不同的虚拟机安装都类似，主要就是配置”类型“，之后就是设置内存、磁盘等等

