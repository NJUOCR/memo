# 小组备忘录

## 服务器使用说明
> 配置`host`:<br />**windows**: C:\Windows\System32\drivers\etc\hosts;<br />**linux**: /etc/hosts <br /> 设置新的主机名 `192.168.68.38 nju-vm`

**tensorflow-docker**:
1. ssh登录，密码`123`
``` shell
ssh -p 222 root@nju-vm
```
2. 文件资源web接口 `http://nju-vm:1234`
3. tensorboard `http://nju-vm:2345

### 使用时请注意
1. 代码放在 `/usr/local/src` 目录下
2. 数据放在 `/usr/local/src/data` 目录下，该目录下的文件可以通过web接口访问


## 注意
### 不要把`.idea`目录下的东西`add`进git
每个人的IDE配置不一样，所以这个目录下的文件会发生冲突，导致项目完全更新不了。删除它们需要以下步骤：
1. 因为其他成员的`.idea`目录是`untracked`状态，所以不能直接解决冲突，也不能直接删除；
2. 要退出IDE，再删除它们。（否则刚一删除就又生成了）
3. 然后使用命令行`update`整个项目，这个时候会把`.idea`目录`pull`下来
4. 当然这个`pull`下来的`.idea`配置目录会导致IDE出现各种奇怪的错误（因为它们不是你的配置文件）
5. 继续用命令行`git rm -r .idea`删除，并提交`git commit -m '' .`，最后`git push`
6. 打开IDE，继续工作

> 是不是很麻烦……所以不要`add`它们，它自动弹出来一定要看一下
---
> 以后的项目我会我加`.gitignore`

## 页面分类任务
[纲要](./page_classification.md)

### TODO
1. 去除页面上的红色像素（它们是水印）

### FIXME
1. 生成文字图片时，文字超出边界
## 资源

### 
1. pytorch开源CRNN https://github.com/Sierkinhane/crnn_chinese_characters_rec
2. 姓名数据库 https://github.com/wainshine/Chinese-Names-Corpus
3. 汉字数据库 https://www.kaggle.com/ruddfawcett/hanzidb
### 旋转文字校正 
1. cnn: https://d4nst.github.io/2017/01/12/image-orientation/
2. 傅里叶+霍夫变换 http://johnhany.net/2013/11/dft-based-text-rotation-correction/
