---
title: Hello Hexo
tags: Hexo
---
Welcome to [Hexo](https://hexo.io/)! This is your very first post. Check [documentation](https://hexo.io/docs/) for more info. If you get any problems when using Hexo, you can find the answer in [troubleshooting](https://hexo.io/docs/troubleshooting.html).
## Hexo 日常命令解析

#### 创建新的页面或文章

``` bash
$ hexo new "My New Post"
```
命令原型：
```bash
$ hexo new [layout] <title> 
    #layout有三种默认布局:
        #page(be saved to the source) 新的页面
        #post(be saved to the source/_posts) 新的文章
        #draft(be saved to the source/_drafts) 新的草稿
```
注意：
- Hexo 默认以标题做为文件名称，但也可以[修改](https://hexo.io/zh-cn/docs/writing.html)；
- 草稿默认不显示到页面，但也可以通过修改**站点配置文件**中的 `render_drafts ` 的值为true来预览草稿；
- 发布草稿到 `source/_posts` 文件夹的命令是： `$ hexo publish [layout] <title>`；

More info: [Server](https://hexo.io/docs/writing.html)
#### 启动本地服务器

``` bash
$ hexo server #等同于 $ hexo s
```
由于我的blog基于github pages搭建，故这个命令一般用于本地调试。值得一提的是，服务的默认端口是4000，如果该命令执行遇到端口冲突错误，自定义端口的命令是这样：
``` bash
$ hexo server -p 2333 
```
> 文档中有提到，Hexo3.0 把服务器模块独立出来，需要那么单独安装 hexo-server ：
`$ npm install hexo-server --save`

More info: [Server](https://hexo.io/docs/server.html)

#### 生成静态页面文件

``` bash
$ hexo generate #等同于 $ hexo g
```

More info: [Generating](https://hexo.io/docs/generating.html)

#### 提交/发布静态页面

``` bash
$ hexo deploy #等同于 $ hexo d
```

More info: [Deployment](https://hexo.io/docs/deployment.html)

#### 清除生成的静态文件

```bash
$ hexo clean
```
此命令一般用于删除已发布的文章（删除source下的文章文件 + clean + generate），清除缓存文件 (db.json) 和已生成的静态文件 (public文件夹)