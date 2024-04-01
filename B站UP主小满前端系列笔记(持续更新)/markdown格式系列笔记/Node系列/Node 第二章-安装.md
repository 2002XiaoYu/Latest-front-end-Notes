# Node 第二章-安装

> 大家好，我是小余，一名前端程序员。向大家分享最实用的技术笔记
>
> - 以下是我发布文章所在的平台，大家可以任意挑选一个合适的平台进行阅读，您的鼓励是作者前进的动力
>
>   知乎：XiaoYu2002
>
>   掘金：XiaoYu2002
>
>   CSDN：XiaoYu2002-AI
>
>   小红书：小余
>
>   GitHub：[2002XiaoYu (小余) (github.com)](https://github.com/2002XiaoYu)
>
> - 我的微信：XiaoYu2002-AI

### 安装nodejs

- 访问官网进行下载
  - [Node.js — Run JavaScript Everywhere (nodejs.org)](https://nodejs.org/en)

|      LTS       |           Current            |
| :------------: | :--------------------------: |
| 长期支持维护版 | 尝鲜版(也叫实验版、小白鼠版) |

![image-20240401191437762](node-images\image-20240401191437762.png)

- 通常点击这里之后，Node官网会直接根据你的电脑环境，自动选择最适合你的版本，当然你也可以点击左上角的Download选项去下载你想要的其他版本
  - 注意下载下来的安装包格式：
    1. 后缀`.msi`是都配置好的
    2. 后缀`.zip`是一个压缩包，还要自己解压后进行配置

![image-20240401191752030](node-images\image-20240401191752030.png)

- 顺着步骤一步步安装下去就行了

## 检查是否安装成功

- 通过快捷键win+R呼出Windows的运行小窗口，在里面输入cmd(也就是终端)

![image-20240401192203170](node-images\image-20240401192203170.png)

- 输入一下命令检测是否安装成功，这里的v是指英文version(版本)，而node、npm、npx都是安装node之后自带的
  1. npm -v
  2. node -v
  3. npx -v
- 如果有弹出具体的版本号，即为安装成功

![image-20240401192343358](node-images\image-20240401192343358.png)

