---
title: 记一次GitHub Action 部署hexo
date: 2022-11-29 12:08:26
tags:
---
# 前言
部署到服务端能用的前提是本地得先能跑起来（好像说的有道理，好像又是废话）。

 本地需要准备的环境：
 - Git
 - Node.js(Node.js 版本需不低于 10.13，建议使用 Node.js 12.0 及以上版本)
  



# 安装


具备以上条件之后就可以通过`npm`安装hexo。
```bash
npm install -g hexo-cli
```
当然要是你足够熟悉`hexo`和`npm` ,可以进行局部安装。
```bash
npm install hexo
```
安装完成之后就可以执行以下命令在本地跑起来：
```bash
hexo s
```
运行之后控制台能输出以下内容，就表示在本地能够正常跑起来了
```shell
INFO  Hexo is running at http://localhost:4000 . Press Ctrl+C to stop.
```
当然我们可以在自己的`hexo`中通过以下命令新建属于自己的博客
```bash
hexo new post <标题>
```

# 部署github

 ## 登录Github新建一个仓库，仓库名必须为你的Github用户名.github.io。 如：

 ```bash
 #   如我的用户名为 circletop
 #  那么仓库名就是circletop.github.io
 ```

 ## 指定用户名和邮箱

 ```shell
 git config --global user.name 'circletop'
 git config --global user.email '648612680@qq.com'
 ```
 
 ## 设置密钥

点击链接：[github apps密钥设置](https://github.com/circletop)设置个人密钥，密钥可以针对单个项目设置，也可以全局设置。
![setting](https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fupload-images.jianshu.io%2Fupload_images%2F5792743-ceada7e5274fe0d1.gif&refer=http%3A%2F%2Fupload-images.jianshu.io&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1672641254&t=7ccca6492c666c751276ebdf3da74bc6)
![](https://img2020.cnblogs.com/blog/1510044/202007/1510044-20200715165046282-888419686.png)

![](https://img2020.cnblogs.com/blog/1510044/202007/1510044-20200715165248224-1246271940.png)
设置完成密钥之后复制密钥，然后修改项目git地址，添加密钥登陆。如： 
```bash
git https://（xxx密钥@）github.com/circletop/circletop.github.io.git
```
## 设置远程仓库地址

```bash
git remote add origin https://（xxx密钥@）github.com/circletop/circletop.github.io.git
```

## 本地项目远程项目 关联并拉取远程项目到本地


```bash
git fetch
git pull origin/main
```
本步骤完了之后就是正常的代码提交就行了。

#部署github action 实现 CI/CD

> 配置部署密钥
> ![密钥配置](https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimg2020.cnblogs.com%2Fblog%2F1411505%2F202110%2F1411505-20211003112928781-1139832760.png&refer=http%3A%2F%2Fimg2020.cnblogs.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1672641538&t=46d1b48e63ddb8258f3eb5216fb7dfe1)
>

以下为详细配置

```yml
name: Pages

on:
  push:
    branches:
      - master # default branch 更换为你项目提交的分支

jobs:
  pages:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Use Node.js 18.12.1 ## 更换为本地node版本
        uses: actions/setup-node@v2
        with:
          node-version: "18" ## 更换为本地node版本
      - name: Cache NPM dependencies
        uses: actions/cache@v2
        with:
          path: node_modules
          key: ${{ runner.OS }}-npm-cache
          restore-keys: |
            ${{ runner.OS }}-npm-cache
      - name: Install Dependencies
        run: npm install
      - name: Build
        run: npm run build
      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }} # 变更为你配置的项目密钥名称
          publish_dir: ./public
```
更改完配置文件之后 执行aciton
![执行action](https://ask.qcloudimg.com/http-save/4932777/mkkpwsmqo1.png?imageView2/2/w/1620)
以下为执行结果
![执行结果](https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fask.qcloudimg.com%2Fhttp-save%2F4932777%2Fifzhrepeac.png%3FimageView2%2F2%2Fw%2F1620&refer=http%3A%2F%2Fask.qcloudimg.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1672640286&t=d01322d392c6b23060e83dd059cea3f8)

