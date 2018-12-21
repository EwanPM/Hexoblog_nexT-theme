---
title: Hexo + Next主题 + GitHub搭建博客
abbrlink: 16107
tags: [Hexo框架, github, 博客, 教程]
categories: 技术
date: 2018-12-12 00:11:09
---
## 背景
原来自己开发了一个[博客](http://ewanzheng.top)，当时也是为了学习技术，了解实现原理，采用node.js+mysql+js，效果真是不堪入目，也有后台管理，功能基本已经实现，但实在不堪入目，写着写着就没什么动力去搞了，无意中了解发现Hexo框架，比worldpress还优雅，是时候该折腾折腾，嗯，说好直接开工，前前后后折腾了差不多一周，白天上班晚上倒腾；虽然教程步骤，网上都有，Hexo，next官方文档写的也是挺详细的，写一个出来加深印象。
## 需求点
Hexo框架实现功能
- 导航栏：首页，分类，标签，归档，关于我，搜索
- 标题
- 分页
- markdown写作

第三方实现功能
- 评论
- 目录列表
- 留言数统计
- 访客人数
- 字数统计
- 阅读时长
- 发表时间
- 阅读量统计

## 准备工作
之前就已经安装过了**node.js**，**Git**和**npm**，一般我用nvm来管理不同的node.js和npm版本，用终端检查一下他们是否在本机成功安装：
```
git --version
git version 2.20.0
```
```
node -v
v8.11.3
```
为了稳妥起见，我用的版本当前比较稳定的版本，有时候最新版可能不一定是最好的。
## 安装Hexo
装好node.js和git之后，可以通过npm来安装。
```
npm i -g hexo-cli
```
不出什么问题几分钟之后就装好了
## 建立网站文件夹
在本地的电脑新建立文件夹，算是网站的全部内容，在终端输入hexo的相关命令来初始化该文件夹
```
hexo init ewan_zheng  # ewan_zheng是自己的文件名
cd ewan_zheng
npm i
```
初始化完成之后，基本的hexo框架算是搞定了。
## 网站结构
```
├── config.yml
├── package.json
├── scaffolds
├── source
├  ├── _posts
└── themes
```
### config.yml
配置文件，以yml为主，博客名称、关键词、作者、系统语言、主题、常用第三方插件等都在里面
### package.json
包文件，从官方库里安装包插件内容和对应版本会列在里面，基本可以不修改此文件。
### scaffolds
脚手架，三种形式的模板，添加一些自定义标签或者类的内容。
### source
放置博客内容地方，默认只有_post(文章)，后续根据自己需求可以添加自定义文件夹，如Tags，categories，about等
### themes
主题，Hexo会根据这个文件夹生成静态页面。初始状态下只有landscape一个文件夹，即默认主题，后续添加next主题，或者其他自己喜欢主题。
### Hexo常用命令
新建初始文件
```
Hexo init
```
新建文章页面，还有一种是添加草稿，个人认为草稿的意义不大，在本地直接post就是草稿了，编辑完了直接部署上传
```
hexo new post "title"
```
生成静态页面
```
hexo generate
hexo g  # 简写
```
部署
```
hexo deploy
hexo d  # 简写
```
启动服务器，默认地址为`http://localhost:4000`
```
hexo server
hexo s      # 简写 
hexo s -p 5000  # 更换一表5000的端口
hexo s --w  # 启动服务器并且监控状态，有修改就会变化
```
以上就是用的最多的命令，其他的基本用不着，搭建完成之后，在localhost:4000就能看到内容
## 设置语言
在config.yml找到`language:`属性，默认不写是英文，改成`language: zh-Hans`
## front matter
这个可以理解为每篇文章的默认配置
```
---
title: Hexo + Next主题 + GitHub搭建博客
abbrlink: 16107
tags: 
- Hexo框架
- github
categories: 技术
---
```
注意，刚开始是没有tags, categories的，首先需要建立相关的文件夹
```
hexo new page categories
hexo new page tags
```
能在source文件夹中新建立文件夹categories, tags并且生成的index.md，并在`front matter`加上`type: "categories"`, `type: "tags"`
注，标签有两种写法，一种是写成-短划线形式
```
---
title: Hexo + Next主题 + GitHub搭建博客
abbrlink: 16107
tags: 
- Hexo框架
- github
categories: 技术
---
```
一种是写成数组形式
```
---
title: Hexo + Next主题 + GitHub搭建博客
abbrlink: 16107
tags:  [Hexo框架, github]
categories: 技术
---
```

```
---
title: 分类
date: 2018-12-14 22:08:47
type: 'categories'
comments: false
---
```
这里如果添加了comments: false，在该页面不能评论，至于评论功能稍后再说。
给模板添加categories，tages
在scarffolds文件夹里的post.md文件，在`font matter`加上categories，tages，这样新创立的文件都会带有这个属性，只要在post文件写分类就会自动带有分类。
```
---
title: {{ title }}
date: {{ date }}
tags:
categories:
---
```
## 部署
一篇文章写完之后，部署域名到互联网，这里部署到GitHub仓库，当然也可以部署到自己创建的域名。
下载git部署依赖包，终端进入站点目录文件下输入
```
npm i hexo-deployer-git --save
```
### 注册GitHub账号
1.首先注册一个账号，并且建立一个仓库。
![建立仓库](https://ws2.sinaimg.cn/large/006tNbRwgy1fy8ieaxen8j30dy0aujrh.jpg)
2.这里要特别注意，Repository name的名字一定要和Username，即owner名字一样，否则，后期怎么部署都会失败。
![添加仓库](https://ws2.sinaimg.cn/large/006tNbRwgy1fy8ic18iyij31350u0q54.jpg)
3.建立好仓库之后，打开刚新建的hexo文件config.yml，搜索找到部署选项，布置一下路径
```
deploy:
  type: git
  repo: git@github.com:EwanPM/EwanPM.github.io.git
  branch: master
```
这里注意两点：
* 属性与描述之间要有空格，即冒号后面加空格。
* git要关联自己的GitHub库，要配置SSH参数，不然这个功能无法生效。

### Git关联远程仓库
这里以mac系统操作
1.创建SSH，在终端输入
```
cd ~/.ssh
```
如果出现`-bash: cd: ~/.ssh: No such file or directory`说明之前没有用过，继续输入命令
```
ssh-keygen -t rsa -C ewanzheng2012@gmail.com  # 这里填入自己注册github的邮箱名字
```
中途会出现，Overwrite(Y/N)，输入y，表示之前有生成过ssh，新生成的需要覆盖吗，最后出现下面的状况就表示生成成功了
![](https://ws3.sinaimg.cn/large/006tNbRwgy1fy8lcstc9mj30zq0lqdhl.jpg)
确认完毕之后，生成一对秘钥放在文件夹: `/users/ewan/.ssh/`，有两个文件一个私钥(id_rsa)、一个公钥(id_rsa.pub)，私钥保存在本机电脑上，公钥放在服务器上。服务器的公钥和本地私钥匹配才能访问服务器的代码库，一定要妥善保管私钥，一旦私钥外泄，服务员上的代码也被泄露。(有点区块链的意思)
### 在Github上关联自己公钥
打开finder，shift+command+G，输入`/Users/ewan/.ssh/id_rsa`进入ssh目录，用记事本打开id_rsa.pub，复制里面的代码
登陆Github，找到setting，在SSH and GPG keys导航里，点击New SSH key，粘贴刚才的代码
![](https://ws2.sinaimg.cn/large/006tNbRwgy1fyb8n1cxpnj321e0sggpj.jpg)
检测是否关联成功，终端输入
```
ssh -T git@github.com
```
![](https://ws3.sinaimg.cn/large/006tNbRwgy1fy8mgzreo5j319e0a6myn.jpg)
出现上面结果表明已关联成功
最后使用`hexo g && hexo -d`就能能将内容推送到github上了，在github上也能看见自己内容，现在算是完成基本功能了。
## nexT主题
hexo上有很多主题，感谢各路大神开源自己代码，看来看去最终还是选择nexT，优雅，简约，功能挺全的。
### 安装
可以去nexT官网文档去[下载](https://github.com/iissnan/hexo-theme-next/releases)，我是直接下的source，解压之后修改文件名为next放在themes目录下。
### 设置主题
打开hexo目录的config.yml找到theme修改主题为next
```
theme: next
```
### 配置next主题
进入刚才的next文件夹，找到config.yml，后面有关配置next主题基本都在这个文件修改
[官方文档](http://theme-next.iissnan.com/getting-started.html)有介绍很多功能，默认状态下config.yml很多功能基本注释掉的，根据自己需求，取消注释并补全信息即可。
#### 主题风格
打开配置文件config.yml，搜索关键字`scheme`，将需启动的`scheme`前面注释取消即可，这里有4种主题，每次只能打开一种，个人比较喜欢mist，muse的简约版本，整洁优雅，其它的效果可以尝试。
#### 评论
自己尝试了评论`Disqus`,`changyan`, `多说`, `来必力`感觉都不好使，多说，畅言已经停止更新，这里用Valine，再次验证了那句话，高手在民间，画面简洁，简洁到没有后台，删除和管理评论直接操作数据库。
到[LeanCloud](https://leancloud.cn/)创建一个开发版(目前免费)。
注册好之后进入，新应用名称->开发版->创建即可
![](https://ws4.sinaimg.cn/large/006tNbRwgy1fya0piih5xj30xe0osmy6.jpg)
点击储存->创建Class，弹出创建Class对话框，Class名称要写`Comment`，ACL权限更改为无限制。
设置->安全中心->服务开关，打开数据存储，其它的都可以关掉，我试了短信服务和推送服务暂时都失效的。
![](https://ws1.sinaimg.cn/large/006tNbRwgy1fya17kkpvnj31b60r275y.jpg)
设置->安全中心->安全域名添加，每行一个域名，多个域名换行，要注意格式。
![](https://ws4.sinaimg.cn/large/006tNbRwgy1fya1bodp87j31900d0dg9.jpg)
设置->应用key
最重要的一步了，复制这里的app ID和App Key
![](https://ws1.sinaimg.cn/large/006tNbRwgy1fya1ezx19ej31r80rkdim.jpg)
在next主题配置文件里，搜索valine，把刚才的appid和appkey复制到对应的属性，enable: true
![](https://ws2.sinaimg.cn/large/006tNbRwgy1fya1jaepgaj31a20j4mza.jpg)
运行`hexo s`看看效果
![](https://ws3.sinaimg.cn/large/006tNbRwgy1fya1k88wopj314a0rcglt.jpg)
最后是关于[邮件提醒](https://github.com/xCss/Valine/wiki)，官方文档有介绍，具体操作如下
![](https://ws4.sinaimg.cn/large/006tNbRwgy1fya1t6aicmj311y0diwez.jpg)

```
你在{{appname}} 的评论收到了新的回复

<p>Hi, {{username}}</p>
<p>
你在 {{appname}} 的评论收到了新的回复，请点击查看：
</p>
<p><a href="https://ewanpm.github.io/" style="display: inline-block; padding: 10px 20px; border-radius: 4px; background-color: #3090e4; color: #fff; text-decoration: none;">马上查看</a></p>
```
回复功能算是完毕了，留言数统计也会显示在副标题的描述中。
#### 文章阅读量统计数据
刚刚在LeanCloud建立了新帐之后，创建Class，弹出对话框，Class名称要写`Counter`，ACL权限更改为无限制，完成之后，左边会多出Counter栏目，在`主题配置文件`，config.yml搜索`leancloud_visitors`，填入刚才的AppID及AppKey，`enable: true`，`hexo s`重新生成刚部署Hexo，就能看到文章阅读量统计了
>记录文章访问量的唯一标识符是文章的发布日期以及文章的标题，因此请确保这两个数值组合的唯一性，如果你更改了这两个数值，会造成文章阅读数值的清零重计。 --[Doublemine](https://notes.doublemine.me/2015-10-21-%E4%B8%BANexT%E4%B8%BB%E9%A2%98%E6%B7%BB%E5%8A%A0%E6%96%87%E7%AB%A0%E9%98%85%E8%AF%BB%E9%87%8F%E7%BB%9F%E8%AE%A1%E5%8A%9F%E8%83%BD.html#%E9%85%8D%E7%BD%AELeanCloud)
上面的这些文章还介绍了一些刷访问量的目的，这里就不多介绍了。
#### 添加搜索功能
这里安装algolia，去[官网](https://www.algolia.com/)注册，我选择github登录，有14天使用所有功能，若未续费则自动降级为免费用户，每个月只能有100,000次请求API。
安装`npm install hexo-algolia`包
```
npm install hexo-algolia@1.2.4 --save  # 这里安装的版本为，1.2.4
```
目前最新的版本为1.3.0，试用了一下，搜索出来的结果无法跳转至正确的路径，改url也没有用。
获取API keys
登录后新建一个index name，后面会用得到
![](https://ws2.sinaimg.cn/large/006tNbRwgy1fyb8ebk1wuj31g80hkdgw.jpg)
之后选择API keys->YOUR API KEYS，记录复制Application ID, Search-Only API Key, Admin API Key
![](https://ws4.sinaimg.cn/large/006tNbRwgy1fyb8gz3txuj31ke0u0778.jpg)
前往`站点配置文件`，打开config.yml添加以下代码
```
# Algolia Search API Key
algolia:
  applicationID: 'Application ID'
  apiKey: 'Search-Only API Key'
  indexName: '输入刚才创建index name'
```
修改Algolia搜索ACL(访问控制列表)
和之前在leancloud一样，创建新的class需要把权根改为无限制，打开ALL API KEYS
![](https://ws2.sinaimg.cn/large/006tNbRwgy1fyb8u09ja2j31sw0u0acu.jpg)
![](https://ws2.sinaimg.cn/large/006tNbRwgy1fyb97rcp83j30xo084q38.jpg)
添加上面的选项，然后保存。
操作完成之后在终端输入以下命令
```
export HEXO_ALGOLIA_INDEXING_KEY=Search-Only API key  #(windows为set) (Powershell 用 $env:) 
export HEXO_ALGOLIA_INDEXING_KEY  #查看是否设置成功如果没有值就设置失败  #(Mac和git bash 为 export) (Powershell 用 $env:) 
hexo algolia
```
![](https://ws2.sinaimg.cn/large/006tNbRwgy1fyb9blo4ctj31600hcacy.jpg)
前往`主题配置文件`，搜索找到Algolia Search，修改以下代码
```
# Algolia Search
algolia_search:
  enable: true
  hits:
    per_page: 10
  labels:
    input_placeholder: 请搜索关键字
    hits_empty: "没有找到结果: ${query}"
    hits_stats: "找到${hits}条结果(用时${time}ms)"
```
搜索找到URL，把URL地址更改为
```
# URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: https://ewanpm.github.io/  # http://yoursite.com
root: /
permalink: :year/:month/:day/:title/
permalink_defaults:
```
`hexo s`刷新，发现已经可以使用了。
#### 网站页脚添加总访问量
找到路径，`themes/next/layout/_partial/footer.swig`中添加busuanzi.js，代码如下
```
<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>
```
注意：站点总访问量是用不蒜子来统计的，近期的官方文档的域名过期了，js的引用可能有问题，接下来找到文件路径`/theme/next/layout/_third-party/analytics/busuanzi-counter.swig`，找到域名并替换为
```
<script async src="https://busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>
```
安装标签
算法a：pv的方式，单个用户连续点击n篇文章，记录n次访问量。
```
<span id="busuanzi_container_site_pv">
    本站总访问量<span id="busuanzi_value_site_pv"></span>次
</span>
```
算法b：uv的方式，单个用户连续点击n篇文章，只记录1次访客数。
```
<span id="busuanzi_container_site_uv">
  本站访客数<span id="busuanzi_value_site_uv"></span>人次
</span>
```
这里采用第二种算法b。
在`主题配置文件`，config.yml添加以下内容
```
 # visitors count
  counter: true
```
在`themes/next/layout/_partial/footer.swig`中添加以下代码
```
{% if theme.footer.counter %}
    <script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>
    <span class="post-meta-divider">|</span>
    <span id="busuanzi_container_site_uv">本站访客数<span id="busuanzi_value_site_uv"></span>人</span>
{% endif %}
```
大功造成。
还有一点，在本地运行会发现访问量数字巨大，这是因为
>这是由于不蒜子用户使用一个存储空间，所以使用localhost:4000进行本地预览的时候会导致数字异常，这是正常现象，只需要将博客部署至云端即可恢复正常。

至此，基本实现了想要的功能，终于可以开心地写写东西了，后面陆续把原来文章迁移到这里，另外还有一些优化和其它功能，等闲了一段时间后再来试试。

***
## 参考
https://valine.js.org/visitor.html
https://ioliu.cn/2017/add-valine-comments-to-your-blog/
https://hexo.io/docs/
http://theme-next.iissnan.com/getting-started.html
https://www.jianshu.com/p/21c94eb7bcd1
https://www.jianshu.com/p/c311d31265e0
