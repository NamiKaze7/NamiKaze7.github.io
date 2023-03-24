# 跳板机SFTP使用指南

### 一、SFTP是什么

跳板机SFTP是官方协议的实现，概念信息可查阅：[百度百科](https://baike.baidu.com/item/SSH文件传输协议/2974699?fromtitle=SFTP&fromid=1184182&fr=aladdin)。

一句话理解，SFTP是文件中转站，相当于**《百度网盘》**，理解了如何在多台电脑之间从百度网盘上传下载文件，即理解了SFTP使用方式。



### 二、文件保留时效

每个用户在创建跳板机账号后，默认开通SFTP账号空间，具有个人的存储空间，方便在本地电脑与远程服务器之间传输小文件（强烈不建议超过10GB的文件使用，不支持断点续传）；

由于每个账号后端的文件是存储在公司对象存储S3服务上，过多无效的文件会带来存储资源浪费，所以使用时**不建议将重要且需要长效保存的文件**存放在SFTP空间：

SFTP文件保留时效

1. 单文件小于100M 保留3年
2. 单文件小于5G 保留 1年
3. 单文件大于5G 保留 6个月
4. 文件夹不清理
5. 离职账号立刻清理所有文件



100M5G0M∞文件大小保留3年保留12个月保留6个月



### 三、SFTP使用指南

浏览器终端下不支持SFTP访问！！！

浏览器终端下不支持SFTP访问！！！

浏览器终端下不支持SFTP访问！！！

什么是浏览器终端？就是你在浏览器里面登录目标机器。

怎么办，我一定要用SFTP传输文件，请接着看教程：

#### **2.1 如果你是Mac笔记本**

通常系统默认自带终端连接工具，或者电脑安装过Iterm工具也可以

#### **2.2 连接至SFTP服务器**

请输入跳板机的密码，而不是大象的密码（除非两个密码你设置一样的），[忘记密码如何重置](https://km.sankuai.com/page/125641680#id-忘记密码)

1. 员工账号使用：jumper.sankuai.com

2. 外包账号使用：jumper-visitor.sankuai.com

   ```shell
   sftp zhanghaozhou@jumper.sankuai.com
   ```

   



#### 2.3 如何将服务器文件下载到Mac

将服务器文件上传至SFTP空间：

将服务器文件下载至Mac笔记本中：

![1933255264](/Users/zhz/Documents/1933255264.png)

#### 2.3 如何将Mac文件上传至服务器

将2.2章节的过程反过来操作，简单说两句：

1. Mac上登录SFTP，将文件put，上传至SFTP空间
2. 跳板机登录目标服务器，在目标服务器上登录SFTP，将文件get，下载至目标服务器



#### 2.4 SFTP 常用命令

- 登录命令：sftp [misid@jumper.sankuai.com](mailto:misid@jumper.sankuai.com)

例如： sftp [alex.wan@jumper.sankuai.com](mailto:alex.wan@jumper.sankuai.com)

1. 上传本地文件命令： put filename
2. 下载服务器文件：get filename

- SFTP空间命令:
  1. ls 查看远程文件
  2. pwd 查看当前所在远程目录
  3. get 下载远程文件
  4. rm 删除远程文件
  5. mkdir 创建远程文件夹
  6. rmdir 删除远程空文件夹
  7. bye 退出远程系统
- SFTP空间下操作本地磁盘的命令：
  1. lls 查看本地文件夹（当你要上传文件时检查下当前文件夹目录）
  2. lcd 切换本地文件夹
  3. put 上传本地文件夹里的文件
  4. lpwd 查看当前本地文件夹所在目录常用的几个命令就这些了，如果不清楚的可以help随时查看支持的命令 

更多命令可输入help查看



#### 2.5 本地上传文件

- 先使用fillezilla，主机：sftp://jumper.sankuai.com，用户名：mis号，快速连接后可进入sftp空间，图片左侧为本机目录，图片右侧为sftp空间目录

- 将本地文件拖拽到sftp空间中

- 登陆jumper，进入服务器。

- 登陆sftp空间：sftp mis@jumper.sankuai.com
- 

- get filename.txt (从sftp空间中上传到服务器上)