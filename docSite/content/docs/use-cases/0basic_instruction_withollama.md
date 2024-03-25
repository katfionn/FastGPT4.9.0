---
title: "使用Ollama对接fastgpt"
description: "喂饭级教程"
icon: "sign_language"
draft: true
toc: true
weight: 501
---


# 安装清单

* [ ] 安装FastGPT

  * [ ] 安装fastgpt 软件主体
  * [ ] 初始化 Mongo 副本集
  * [ ] 调整端口

* [ ] 安装M3E向量模型
* [ ] 安装OneAPI

  * [ ] 安装mySQL
  * [ ] 安装Redis
  * [ ] 安装OneAPI软件主体
* [ ] 安装Ollama

  * [ ] 选择AI模型
  * [ ] 安装Ollama并载入模型
* [ ] 参数配置与调整

  * [ ] ollama与OneAPI联调
  * [ ] 在OneAPI平台添加M3E模型
  * [ ] OneAPI适配fastGPT的config配置

安装清单没有顺序要求，但阅读教程时建议由上到下阅读。


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

  


# 总结

  本教程可以快速帮助没有任何AI基础理论、Linux系统基础知识、服务器运维知识的小白上手安装使用，帮助像我一样傻乎乎超级小白入门体验AI产品。

  本教程的不足在于有以下几点，欢迎大佬帮忙优化更好的解决方案：

1. 主机装在虚拟机中，性能有损耗，没发挥硬件的完整实力；
2. 运行的模型依赖ollama，自由度和可玩度没有直接部署的更高；
3. 仅清楚配置方法，ollama里面的对应模型的各种能力参数不够了解，如上下文理解有多少token均不清楚，因此在配置fastgpt的config文件里配置可能不够完善；
4. 每次重构fastgpt容器时，需要手动修改其“环境变量”里的端口号，3000改为3020，否则打不开fastgpt。
