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

- [安装M3E向量模型](#安装M3E向量模型)

- [安装OneAPI](#安装OneAPI)
  - [安装mySQL](#一安装mysql)
  - [安装Redis](#二安装redis)
  - [安装OneAPI软件主体](#安装oneapi)
    
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
- 虚拟机IP：192.168.1.5
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
5. 如果在国内，下一步是修改pg和fastgpt的镜像源，替换默认源为阿里的镜像源：把红框里的内容复制 → 选中绿框中的内容 → 粘贴，然后点击保存。
    ![image](https://github.com/katfionn/FastGPT/assets/136874302/ac41ab43-f281-490e-a0e8-4dc2a2ced0ed)
    ![image](https://github.com/katfionn/FastGPT/assets/136874302/58d38e2d-4594-480d-8486-1217254f30a7)
6. <a id="ft.2.5"></a>删除默认文档里的前四行注释，如图：
    ![image](https://github.com/katfionn/FastGPT/assets/136874302/fcf8c177-79a7-4061-afa6-9d83b5b12e23)
7. 删除oneapi相关内容，后面单独配置，在文档最下面
    ![image](https://github.com/katfionn/FastGPT/assets/136874302/cd6122d0-1131-4e10-9661-aa6bea606877)
8. 点击左下角“保存” 然后返回上一个页面
9. 在1panel中，按以下步骤打开终端界面
    ![image](https://github.com/katfionn/FastGPT/assets/136874302/f10bcf17-6dea-458d-9196-8ddd60e871fd)
    ![image](https://github.com/katfionn/FastGPT/assets/136874302/f139cce7-c240-46ce-9f54-be19720970ae)

    > 如果按图中指引，测试连接失败，可能是因为你的Linux不支持直接使用root用户进行远程ssh登录，改成普通用户即可，例如我的用户名叫banana，我就填写banana；
    > 注意，普通用户的密码和root的密码是不同的。如果用普通用户登录，那么你需要看第9点，反之可以跳过第9点。

10. 在终端界面，输入su，进入root用户模式
    ![image](https://github.com/katfionn/FastGPT/assets/136874302/be2b4a15-7343-4379-9d2e-625a2a0b7808)
11. 输入命令：

     ```shell
     cd /home/banana/fastgpt&&docker-compose pull
     ```
12. 开始拉取镜像：
     ![image](https://github.com/katfionn/FastGPT/assets/136874302/9de35906-47fa-4be8-95aa-1a7c93342dae)
    静待30分钟左右（等待时间取决于网速），然后在1panel观察容器是否正常：
    ![image](https://github.com/katfionn/FastGPT/assets/136874302/d4d6a1b9-ce20-4fd2-aac6-28d554af7227)
13. 镜像拉取完毕：

     ![image](https://github.com/katfionn/FastGPT/assets/136874302/e872cd35-cf7b-4627-a1b5-4dee14a699d2)

     通常来说，回到命令行了，就算是拉取完了，也就是说你可以继续打字了，说明[第5步](#ft.2.5)操作内容完成了
14. 使用下面的命令构建容器：

     ```shell
     docker-compose up -d
     ```
15. 前往1panel容器界面，查看容器是否正常运行，逐个查看日志是否有报错
16. 一般情况下正常启用的容器长这样：
     ![image](https://github.com/katfionn/FastGPT/assets/136874302/4072e96b-0e13-4fa8-b6ca-c660d8e78e0c)
17. 到这，FastGPT的软件主体就安装完成了，接下来进行数据初始化操作。

## 三、初始化 Mongo 副本集

1. 在1panel中，进入容器列表，找到mongo容器，点击操作列的“终端”按钮
    ![image](https://github.com/katfionn/FastGPT/assets/136874302/c436c2af-1a3d-4452-8a57-78deea4e9dbb)
2. <a id="ft.3.2"></a>在弹窗中，点击“连接”按钮，会进入一个终端界面

    ![image](https://github.com/katfionn/FastGPT/assets/136874302/aeaddaf1-6980-493d-869e-2d3b5c93e98c)
3. 复制下面代码

    ```shell
    mongo -u myusername -p mypassword --authenticationDatabase admin
    ```

    这行命令用于登录mongo数据库。其中，`-u`后面跟的是该数据库用户名，默认为`myusername`；`-p`后面跟的是该数据库密码，默认为`mypassword`。这里对应[安装时](###二、执行安装)的docker-compose文件配置，不修改默认都能对应的上，不懂的话不建议修改，否则可能出现稀奇古怪且很难找人解决的问题、他人排查起来也很困难。
4. <a id="ft.3.4"></a>粘贴上一步的代码  到 [第2步](#ft.3.2)打开的终端界面，可以直接鼠标右键粘贴。随后终端界面会出些类似下面的文字，并且在最下面，命令行开头会有一个尖括号，说明上一步命令执行成功了
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

1. 由于不知名的原因，安装完容器后，在环境变量里会多出一个“配置端口”的参数，这里也需要改成3020，和[前面](#ft.2.3)保持一致。在容器界面找到fastgpt
2. 把鼠标光标悬浮在 fastgpt 右边操作列 里 “更多”上面，找到“编辑”，单击它，如下图
   ![image](https://github.com/katfionn/FastGPT/assets/136874302/462c5631-f728-44cc-a46c-4df576de917a)
   然后应该出现下图的侧边弹窗：

   ![image](https://github.com/katfionn/FastGPT/assets/136874302/3217fea3-43b3-4c35-b225-d995ea44c0dd)
3. 把弹窗向下滚动到底部，在“环境变量”这块内容里 → 找到`PORT=3000` → 修改3000为3020 → 点击右下角的“确定”
   ![image](https://github.com/katfionn/FastGPT/assets/136874302/d75e5853-2fa2-413d-bb61-0f4f4f7f8ddc)
4. 在弹出的弹窗中，点击确定：

   ![image](https://github.com/katfionn/FastGPT/assets/136874302/11767a66-8b99-484e-8fdd-ac3e5dafc0e0)
5. 等待容器重启，fastgpt此处的端口号不一定会同步显示，：
   ![image](https://github.com/katfionn/FastGPT/assets/136874302/70860510-c0bc-4ba2-b8cd-5d2e5aab669a)
6. 测试端口是否正常：打开浏览器新标签，输入你虚拟机的IP+端口，尝试访问fastgpt。如：我IP是192.168.1.5，端口是3020，那么我就在浏览器的地址栏输入：
   ```
   192.168.1.5:3020
   ```
7. 看到下图界面，说明配置成功了:
   ![image](https://github.com/katfionn/FastGPT/assets/136874302/f3de37ff-a36c-4b89-9966-e81326b9ed90)

## 五、登录后台，验证FastGPT是否安装成功

用账号密码进行登录即可，依照以上步骤，不修改任何东西，那么你的默认账号密码为：
```
账号：root
密码：1234
```
登陆后出现如下提示即为完成FastGPT安装：

![image](https://github.com/katfionn/FastGPT/assets/136874302/063d9923-3b97-49d3-9963-fd49ed4cd26f)

# 安装M3E向量模型

1. 根据实际情况选择下面任一命令，通过容器安装，先拷贝好安装命令
    1. 用CPU运行
       ```
       docker run -d -p 6008:6008 --name=m3e-large-api registry.cn-hangzhou.aliyuncs.com/fastgpt_docker/m3e-large-api:latest
       ```
    2. 用GPU运行
       ```
       docker run -d -p 6008:6008 --gpus all --name=m3e-large-api registry.cn-hangzhou.aliyuncs.com/fastgpt_docker/m3e-large-api:latest
       ```
    3. 源镜像
       ```
       docker run -d -p 6200:6008 --name=m3e-large-api stawky/m3e-large-api:latest
       ```
2. 连接上机器的SSH终端界面，进入root模式
3. 将上面复制的命令粘贴到命令行，然后回车，出现以下界面说明命令执行成功
   ![image](https://github.com/katfionn/FastGPT/assets/136874302/9ffa8b13-d1e4-49ec-8940-1a0a684b3eb9)
   出现下图界面，说明安装应该完成了，下一步前往1panel“容器”界面查看
   ![image](https://github.com/katfionn/FastGPT/assets/136874302/43479c55-d105-4269-ab65-bdabe28e219a)
4. 前往1panel界面查看，显示已启动、日志无异常，则先当作他安装成功了，需要后面的步骤才能验证他能不能用
   ![image](https://github.com/katfionn/FastGPT/assets/136874302/a2ebd1c3-8a77-4e64-9e55-a276a376aed8)
   ![image](https://github.com/katfionn/FastGPT/assets/136874302/976a51a6-3598-483a-86ba-944f39e4c949)
5. 修改M3E的容器重启策略为“一直重启”，这步如果不操作，每次开机后需要手动启用容器：
   在1panel的容器界面：找到M3E的容器 → 然后鼠标放在“更多”上 → 点击弹出列表里的“编辑” ，如下面图一所示：
   ![image](https://github.com/katfionn/FastGPT/assets/136874302/45d99de1-316e-4885-b734-f26f0e50f0cb)
   然后找到“重启规则”板块 → 选择“一直重启” → 点击右下角“确定” → 然后在弹出的提示弹窗中再点击“确定”，如下图所示：
   ![image](https://github.com/katfionn/FastGPT/assets/136874302/9add86b6-d0d3-4437-a8a5-c4dd1e122f41)
6. 到这为止，M3E向量模型安装完成

### 记录关键信息 ((20240320013901-s0zz5y8 "*"))


* M3E模型的名字

```shell
m3e
```

* M3E模型的Base_Url
  其中**192.168.1.5**是我的IP，可以替换成你自己的

```shell
http://192.168.1.5:6008
```

* M3E模型的密钥（默认）

```shell
aaabbbcccdddeeefffggghhhiiijjjkkk
```

# 安装OneAPI

> * OneAPI的docker文件官方地址：https://github.com/songquanpeng/one-api/blob/main/docker-compose.yml ((20240321012024-wj66rwb "*"))
> * 鉴于多次尝试，用官方文件直接安装基于容器的oneapi时，都会卡在mysql用不了的情况(个人有这需求)；所以最终解决办法是：先手动安装好mysql、手动建好与官方配置里一模一样的数据库，然后再安装oneapi。
> * 通过1panel应用商店安装数据库，有利于像我一样的超级小奶白，通过友好且可视化的界面备份/回滚数据库。

## 一、安装mySQL

1. 进入1panel后台，找到应用商店的“全部 - 数据库”分类，找到MySQL并点击“安装”：
   ![image](https://github.com/katfionn/FastGPT/assets/136874302/0cf181f3-c9bb-4307-a477-8345d1db06ba)
2. 在弹窗中，修改数据库密码为自己熟悉的 → 勾选“端口外部访问” → 点击右下角的确定
   ![image](https://github.com/katfionn/FastGPT/assets/136874302/f4478281-739a-4ee1-958c-f6e1d310a26d)
3. 然后会进入安装过程，等待安装完成
4. 如果安装完成了，你可以在“应用商店 - 已安装”分类下找到状态为“已启动”的应用，应能看到mySQL应用已启动：
   ![image](https://github.com/katfionn/FastGPT/assets/136874302/eb1984f3-468e-4be1-89bb-d1c2ebe402d9)
5. 对照opeapi的官方docker-conpose文档，按下列信息与图中的步骤，创建数据库
   `数据库名称：one-api`  `用户名：oneapi` `密码：123456`
   具体操作如下图：
   ![image](https://github.com/katfionn/FastGPT/assets/136874302/56a21289-01e1-4cb6-9be2-8d2553e075e4)
   新建成功后，会出现以下界面：
   ![image](https://github.com/katfionn/FastGPT/assets/136874302/938865d1-4d88-4cad-865f-f5e734edd992)
   

## 二、安装redis

1. 进入1panel后台，找到应用商店的“全部 - 数据库”分类，找到Redis并点击“安装”：
   ![image](https://github.com/katfionn/FastGPT/assets/136874302/e78cb4a4-bd01-4208-ba0e-c9271b57363b)
2. <a id="oa.2.2"></a>在弹窗中，修改数据库密码为自己熟悉的 → 勾选“端口外部访问” → 点击右下角的确定
   ![image](https://github.com/katfionn/FastGPT/assets/136874302/210b5220-38c6-4ba7-9fdc-deee7d54d6a1)
3. 等待数分钟后(取决于电脑性能和网络速度)，就安装完成了，可以在应用商店的“已安装”分类中，找到Redis，如下图：
   ![image](https://github.com/katfionn/FastGPT/assets/136874302/a233a4a1-0d3c-41b2-93f3-97c03b92ff6a)
4. 到这，Redis就安装好了，需要记录[第2步](#oa.2.2)的信息，例如案例中我的是：
   用户(默认)：root
   密码：abc123
   端口：6379

## 三、安装OneAPI

1. 打开你的alist → 然后点开/home/banana目录 → 新建名叫“redis”的文件夹 → 从官方项目[下载yml文件](https://github.com/songquanpeng/one-api/blob/main/docker-compose.yml "官方yml文件")
2. 由于本地事先装好了数据库，所以下载了官方的文档后，我们需要做一些修改，下面是修改好之后的成品：

    ```yml
    version: '3.4'

    services:
      one-api:
        image: "${REGISTRY:-docker.io}/justsong/one-api:latest"
        container_name: one-api
        restart: always
        command: --log-dir /app/logs
        ports:
          - "3000:3000"
        volumes:
          - ./data/oneapi:/data
          - ./logs:/app/logs
        environment:
          - SQL_DSN=oneapi:123456@tcp(192.168.1.5:3306)/one-api  # 修改此行，或注释掉以使用 SQLite 作为数据库
          - REDIS_CONN_STRING=root:abc123@tcp(192.168.1.5:6379)//redis
          - SESSION_SECRET=random_string  # 修改为随机字符串
          - TZ=Asia/Shanghai
    #      - NODE_TYPE=slave  # 多机部署时从节点取消注释该行
    #      - SYNC_FREQUENCY=60  # 需要定期从数据库加载数据时取消注释该行
    #      - FRONTEND_BASE_URL=https://openai.justsong.cn  # 多机部署时从节点取消注释该行
        healthcheck:
          test: [ "CMD-SHELL", "wget -q -O - http://localhost:3000/api/status | grep -o '\"success\":\\s*true' | awk -F: '{print $2}'" ]
          interval: 30s
          timeout: 10s
          retries: 3
    ```

    可以直接复制以上内容 → 在alist打开你的yml文件 → 在alist上方选择编辑文件 → 键盘上同时按下Ctrl+A → 键盘上同时按下Ctrl+V → 点击左下角的保存，记得修改对应`安装环境`，如我的虚拟机ip是`192.168.1.5`，而你的是`192.168.20.12`，就要替换上面文本中的`192.168.1.5`为`192.168.20.12`。可以使用Ctrl+F搜索/替换。

    实际你需要修改的就是**oneapi块**里、**environment**的sql和redis的ip：

    ```yml
          - SQL_DSN=oneapi:123456@tcp(192.168.1.5:3306)/one-api  # 修改此行，或注释掉以使用 SQLite 作为数据库
          - REDIS_CONN_STRING=root:abc123@tcp(192.168.1.5:6379)//redis
    ```
3. 在1panel的“((20240319225622-04s2hbe "主机-终端"))”界面，粘贴下列指令到终端中并回车，拉取镜像

    ```
    docker-compose pull
    ```
    ![image](https://github.com/katfionn/FastGPT/assets/136874302/bf6a6f48-5e7e-42c8-b7f9-81ac19e374a4)
4. 接着直接粘贴下列指令到终端中并回车，在后台运行容器

    ```
    docker-compose up -d
    ```

    等命令行运行完、再次出现输入提示，就可以在1panel的容器界面看到这个容器正常运行了
    ![image](https://github.com/katfionn/FastGPT/assets/136874302/b3580425-bde3-4022-880c-5752a11b48a5)
5. 浏览器中输入地址“http://192.168.1.5:3000”验证一下是否可以正常访问
   ![image](https://github.com/katfionn/FastGPT/assets/136874302/7c72b06e-1f05-4fe9-a8d7-962e2bb3eca0)
   出现上图界面，代表oneapi已安装完成，可使用默认账号密码登录：
   
   账号
   ```
   root
   ```
   密码
   ```
   123456
   ```




# 总结

  本教程可以快速帮助没有任何AI基础理论、Linux系统基础知识、服务器运维知识的小白上手安装使用，帮助像我一样傻乎乎超级小白入门体验AI产品。

  本教程的不足在于有以下几点，欢迎大佬帮忙优化更好的解决方案：

1. 主机装在虚拟机中，性能有损耗，没发挥硬件的完整实力；
2. 运行的模型依赖ollama，自由度和可玩度没有直接部署的更高；
3. 仅清楚配置方法，ollama里面的对应模型的各种能力参数不够了解，如上下文理解有多少token均不清楚，因此在配置fastgpt的config文件里配置可能不够完善；
4. 每次重构fastgpt容器时，需要手动修改其“环境变量”里的端口号，3000改为3020，否则打不开fastgpt。
