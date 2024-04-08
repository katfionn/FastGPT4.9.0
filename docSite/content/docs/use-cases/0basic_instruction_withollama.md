---
title: "使用Ollama对接fastgpt"
description: "喂饭级教程"
icon: "sign_language"
draft: true
toc: true
weight: 501
---


# 安装清单

- [安装FastGPT](#安装FastGPT)

  - 安装fastgpt 软件主体
  - 初始化 Mongo 副本集
  - 调整端口

- 安装M3E向量模型

- 安装OneAPI
  - 安装mySQL
  - 安装Redis
  - 安装OneAPI软件主体
    
- 安装Ollama
  - 选择AI模型
  - 安装Ollama并载入模型

- 参数配置与调整
  - ollama与OneAPI联调
  - 在OneAPI平台添加M3E模型
  - OneAPI适配fastGPT的config配置

> 安装清单没有顺序要求，但阅读教程时建议由上到下阅读。


# 基础软件

* alist

  * 所有文件编辑都将通过alist或1panel进行
  * [alist一键安装教程](https://alist.nn.ci/zh/guide/install/script.html "alist一键安装教程")
* 1panel

  * 所有终端、命令行操作，都将通过1panel进行
  * 所有文件编辑都将通过alist或1panel进行
  * [1panel一键安装教程](https://1panel.cn/docs/installation/online_installation/ "1panel一键安装教程")


# 教程中的安装环境

> 以下是教程中的示例数据相关信息，请在实际使用时替换成你自己的内容

- 主机：基于Vmware的Linux虚拟机、网卡选择了“桥接模式：直接连接物理网络”(不勾选“直接复制物理网络连接状态)
- Linux配置：30G运存、100G硬盘、32核心CPU、无显卡
- 宿主机IP：192.168.1.2
- 虚拟机IP：192.168.1.3
- Linux 普通用户：banana
- Linux 普通用户密码：ABCD1234
- Linux root 用户密码：Abc123456
- 网络：国内大陆地区一线城市电信宽带，本教程不需要魔法


# 安装FastGPT

## 一、获取docker-compose与config文件 ((20240319224308-3luzywh "*"))

#### 获取docker-compose文件

从以下任意一个链接下载docker-compose文件

[https://raw.githubusercontent.com/labring/FastGPT/main/files/deploy/fastgpt/docker-compose.yml](https://raw.githubusercontent.com/labring/FastGPT/main/files/deploy/fastgpt/docker-compose.yml)

[https://github.com/labring/FastGPT/blob/main/files/deploy/fastgpt/docker-compose.yml](https://github.com/labring/FastGPT/blob/main/files/deploy/fastgpt/docker-compose.yml)

#### 获取config文件

从以下任意一个链接下载docker-compose文件

[https://raw.githubusercontent.com/labring/FastGPT/main/projects/app/data/config.json](https://raw.githubusercontent.com/labring/FastGPT/main/projects/app/data/config.json)

[https://github.com/labring/FastGPT/blob/main/projects/app/data/config.json](https://github.com/labring/FastGPT/blob/main/projects/app/data/config.json)

## 二、执行安装

1. 登陆alist，在“/home/banana”目录下建立“fastgpt”目录

    ![image](https://github.com/katfionn/FastGPT/assets/136874302/204e5182-70be-4956-9cb1-1735d83b0e27)
2. 把下载的文件通过alist上传，不需要重命名
    ![image](https://github.com/katfionn/FastGPT/assets/136874302/2e745700-c57f-4d14-a429-46d87b232b16)
3. <a id="ft.2.3"></a>用alist打开docker-compose → 进入编辑模式

    ![image](https://github.com/katfionn/FastGPT/assets/136874302/9513d04c-f3f0-4ff1-9568-5c9215e3a8ae)
鼠标随便点击一下编辑器内 → 键盘同时按下Ctrl+F → 在弹出的搜索框中输入fastgpt、找到fastgpt这一块内容 → 将3000都改为3020 → 点击左下角“保存”。修改的目的是为oneapi保留3000端口。
    ![image](https://github.com/katfionn/FastGPT/assets/136874302/565a1ddc-d3a8-4a92-82f5-af09bead46ab)
    ![image](https://github.com/katfionn/FastGPT/assets/136874302/0a7cc6d5-85fc-46cf-bfac-e40e5171c23d)
    ![image](https://github.com/katfionn/FastGPT/assets/136874302/c9d121e5-6f55-4671-94b1-9b84d7808337)
4. 如果在国内，下一步是修改pg和fastgpt的镜像源，替换默认源为阿里的镜像源：把红框里的内容复制 → 选中绿框中的内容 → 粘贴，然后点击保存。
    ![image](https://github.com/katfionn/FastGPT/assets/136874302/ac41ab43-f281-490e-a0e8-4dc2a2ced0ed)
    ![image](https://github.com/katfionn/FastGPT/assets/136874302/58d38e2d-4594-480d-8486-1217254f30a7)
5. <a id="ft.2.5"></a>删除默认文档里的前四行注释，如图：
    ![image](https://github.com/katfionn/FastGPT/assets/136874302/fcf8c177-79a7-4061-afa6-9d83b5b12e23)
6. 删除oneapi相关内容，后面单独配置，在文档最下面
    ![image](https://github.com/katfionn/FastGPT/assets/136874302/cd6122d0-1131-4e10-9661-aa6bea606877)
7. 点击左下角“保存” 然后返回上一个页面
8. 在1panel中，按以下步骤打开终端界面
    ![image](https://github.com/katfionn/FastGPT/assets/136874302/f10bcf17-6dea-458d-9196-8ddd60e871fd)
    ![image](https://github.com/katfionn/FastGPT/assets/136874302/f139cce7-c240-46ce-9f54-be19720970ae)

    > 如果按图中指引，测试连接失败，可能是因为你的Linux不支持直接使用root用户进行远程ssh登录，改成普通用户即可，例如我的用户名叫banana，我就填写banana；注意，普通用户的密码和root的密码是不同的。
    >

9. 在终端界面，输入su，进入root用户模式
    ![image](https://github.com/katfionn/FastGPT/assets/136874302/be2b4a15-7343-4379-9d2e-625a2a0b7808)
10. 输入命令：

     ```shell
     cd /home/banana/fastgpt&&docker-compose pull
     ```
11. 开始拉取镜像：
     ![image](https://github.com/katfionn/FastGPT/assets/136874302/9de35906-47fa-4be8-95aa-1a7c93342dae)
    静待30分钟左右（等待时间取决于网速），然后在1panel观察容器是否正常：
    ![image](https://github.com/katfionn/FastGPT/assets/136874302/d4d6a1b9-ce20-4fd2-aac6-28d554af7227)
12. 镜像拉取完毕：
     ![image](https://github.com/katfionn/FastGPT/assets/136874302/e872cd35-cf7b-4627-a1b5-4dee14a699d2)
     通常来说，回到命令行了，就算是拉取完了，也就是说你可以继续打字了，说明[第5步](#ft.2.5)操作内容完成了
13. 使用下面的命令构建容器：

     ```shell
     docker-compose up -d
     ```
14. 前往1panel容器界面，查看容器是否正常运行，逐个查看日志是否有报错
15. 一般情况下正常启用的容器长这样：
     ![image](https://github.com/katfionn/FastGPT/assets/136874302/4072e96b-0e13-4fa8-b6ca-c660d8e78e0c)
16. 到这，FastGPT的软件主体就安装完成了，接下来进行数据初始化操作。

## 三、初始化 Mongo 副本集

1. 在1panel中，进入容器列表，找到mongo容器，点击操作列的“终端”按钮
    ![image](https://github.com/katfionn/FastGPT/assets/136874302/c436c2af-1a3d-4452-8a57-78deea4e9dbb)
2. <"ft.3.2"></a>在弹窗中，点击“连接”按钮，会进入一个终端界面
    ![image](https://github.com/katfionn/FastGPT/assets/136874302/aeaddaf1-6980-493d-869e-2d3b5c93e98c)
3. 复制下面代码

    ```shell
    mongo -u myusername -p mypassword --authenticationDatabase admin
    ```

    这行命令用于登录mongo数据库。其中，`-u`后面跟的是该数据库用户名，默认为`myusername`；`-p`后面跟的是该数据库密码，默认为`mypassword`。这里对应[安装时](###二、执行安装)的docker-compose文件配置，不修改默认都能对应的上，不懂的话不建议修改，否则可能出现稀奇古怪且很难找人解决的问题、他人排查起来也很困难。
4. <"ft.3.4"></a>粘贴上一步的代码  到 [第2步](#ft.3.2)打开的终端界面，可以直接鼠标右键粘贴。随后终端界面会出些类似下面的文字，并且在最下面，命令行开头会有一个尖括号，说明上一步命令执行成功了
    ![image](https://github.com/katfionn/FastGPT/assets/136874302/e1d0880b-0b27-47b2-908f-027f276e7f6f)
5. 复制下面的代码

    ```shell
    rs.initiate({
      _id: "rs0",
      members: [
        { _id: 0, host: "mongo:27017" }
      ]
    })
    ```

    **关于以上代码中的** **`host: “mongo:27017”`**  **说明**

    1. mongo:27017 代表指向同一个 docker 网络的 mongo 容器的 27017 服务。因此，如果使用该参数，外网是无法访问到数据库的。
    2. ip:27017 （ip替换成公网IP）：代表通过你的公网IP进行访问。如果用该方法，同时需要修改 docker-compose 中 mongo 的连接参数，因为默认是用 `mongo:27017` 进行连接。
6. 粘贴上一步的代码  到[第4步](#ft.3.4) 的终端界面中，即那个尖括号后面，可以右键粘贴。然后按一次回车，应会出现OK提示，如下图：
    ![image](https://github.com/katfionn/FastGPT/assets/136874302/78d2b8b1-babb-40fd-869e-0ccaf42020c3)
7. 点击弹窗左上角“返回”或 按一次键盘左上角的ESC退出终端界面
    ![image](https://github.com/katfionn/FastGPT/assets/136874302/d6597749-5edc-4ab4-83b5-71718805ebe0)
8. 初始化数据完成

## 四、修改fastgpt端口

1. 由于不知名的原因，安装完容器后，在环境变量里会多出一个“配置端口”的参数，这里也需要改成3020，和[前面](#ft2.3)保持一致。在容器界面找到fastgpt



# 总结

  本教程可以快速帮助没有任何AI基础理论、Linux系统基础知识、服务器运维知识的小白上手安装使用，帮助像我一样傻乎乎超级小白入门体验AI产品。

  本教程的不足在于有以下几点，欢迎大佬帮忙优化更好的解决方案：

1. 主机装在虚拟机中，性能有损耗，没发挥硬件的完整实力；
2. 运行的模型依赖ollama，自由度和可玩度没有直接部署的更高；
3. 仅清楚配置方法，ollama里面的对应模型的各种能力参数不够了解，如上下文理解有多少token均不清楚，因此在配置fastgpt的config文件里配置可能不够完善；
4. 每次重构fastgpt容器时，需要手动修改其“环境变量”里的端口号，3000改为3020，否则打不开fastgpt。
