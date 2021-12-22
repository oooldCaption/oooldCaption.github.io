---
title: 使用Hexo+Coding搭建个人博客
tags: []
id: '70'
categories:
  - - 实用技术
date: 2018-04-17 15:10:01
---

前言 之前博客挂在 github pages 上,回到新疆之后发现访问不了了.一琢磨就买个个主机使用 wordpress搭建了个个人博客,当使用 wordpress 实在是不能提起我的写作欲望,又麻溜的切换到了 hexo.在网上看到了coding 也提供了类似 github pages 的服务 coding-pages,瞬间觉得太好了.于是我又开始了 hexo 之旅;
<!-- more -->
正文

*   环境：　mac　os ＋　hexo3.0　＋ iTerm　＋ Mou　＋ next + homebrew １．　安装非常简单，只需要几分钟即可搞定；

２．　查看你的电脑中有没有安装

*   Node.js
*   Git.
*   如果您的电脑没有安装上诉的程序,可以点击访问安装; \*

好了,如果这些步骤都完成,您就可以安装 hexo 了; npm install -g hexo-cli 建站 执行下面的命令,Hexo 会在文件夹中新建需要的文件 hexo init folder hexo init ~Document/Hexo e.g. cd folder npm install 完成这一步之后,您就可以在您的 hexo 文件夹下执行 hexo g hexo s 在浏览器中访问 http://localhost:4000,这是您就能看到 Hexo 的页面了,是不是很帅!轻轻松松就看到了效果,有没有觉得很有成就感; 当然这个博客是存在于您的本地;先别着急,咱们看一下本地怎么配置,怎么配置主题; 在 Hexo 文件夹下有一个\_config.yml,就叫他 站点配置文件吧;文件不大,加上注释空行也就八十多行; # Hexo Configuration ## Docs: http://hexo.io/docs/configuration.html ## Source: https://github.com/hexojs/hexo/ # Site 这下面的几项配置都很简单，你看我的博客就知道分别是什么意思 title: 两好三坏 #博客名 subtitle: 爱情,两好三坏 #副标题 description: 长恨人心不如水,等闲平地起波澜 #用于搜索，没有直观表现 author: 两好三坏 #作者 language: zh-CN #语言 timezone: #时区，此处不填写，hexo会以你目前电脑的时区为默认值 # URL 暂不配置，使用默认值 ## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/' url: http://opiece.me #域名 root: / permalink: :year/:month/:day/:title/ permalink\_defaults: # Directory 暂不配置，使用默认值 source\_dir: source public\_dir: public tag\_dir: tags archive\_dir: archives category\_dir: categories code\_dir: downloads/code i18n\_dir: :lang skip\_render: # Writing 文章布局等，使用默认值 new\_post\_name: :title.md # File name of new posts default\_layout: post titlecase: false # Transform title into titlecase external\_link: true # Open external links in new tab filename\_case: 0 render\_drafts: false post\_asset\_folder: false relative\_link: false future: true highlight: enable: true line\_number: true tab\_replace: # Category Tag 暂不配置，使用默认值 default\_category: uncategorized category\_map: tag\_map: # Date / Time format 时间格式，使用默认值 ## Hexo uses Moment.js to parse and display date ## You can customize the date format as defined in ## http://momentjs.com/docs/#/displaying/format/ date\_format: YYYY-MM-DD time\_format: HH:mm:ss # Pagination ## Set per\_page to 0 to disable pagination per\_page: 10 #每页显示的文章数，0表示不分页 pagination\_dir: page # Extensions 插件配置，暂时不配置 ## Plugins: http://hexo.io/plugins/ ## Themes: http://hexo.io/themes/ theme: hexo-theme-next #使用的主题，即：themes文件夹下的主题文件夹名 feed: #之后配置rss会用，使用如下配置即可 type: path: limit: # Deployment 用于部署到github，之前已经配置过 ## Docs: http://hexo.io/docs/deployment.html deploy: type: git repo: https://git.coding.net/usename/usename.git #coding仓库地址 branch: coding-pages 再次运行 hexo,看看 hexo 比第一次打开之后顺眼多了,至少变成汉字了吧~哈哈哈哈哈哈 切换主题,主题配置文件 在iTerm进入到 Hexo 目录下,然后执行 git 下载 next 主题 git clone https://github.com/iissnan/hexo-theme-next.git 下载完成后会在 hexo 下的 theme 文件下,文件名夹名为 hexo-theme-next,在站点配置文件中将主题设置为 hexo-theme-next;再次运行 hexo s 查看效果,是不是跟开心啦~~~ 在hexo-theme-next中有\_config.yml,这是是主题的配置文件 你可以在Next 帮助文档查看如何个性化定制; 写作

*   你可以使用下列命令创建一个新的文章:
*   hexo new \[layout\] <title> ,layout 是布局,title 是文章标题; 写作是使用 markdown语法,你可以在MarkDown 语法入门查看; 如果你需要标签功能和分类功能,在终端进入到您的hexo文件夹下 执行:
*   hexo new page "tags" 创建标签页面,在hexo 文件夹下的 \\source\\tags\\index.md 中添加一个 type: “tags”
*   hexo new page categories 创建分类页面,在hexo 文件夹下的 \\source\\categories\\index.md 中添加一个 type: “categories” 然后你也可以在hexo 文件夹下的 \\scaffolds\\post.md 中添加一个上 tags: categories: ,这个 post 是你每次书写的时候的一个模板;添加后每次创建行的文章就不用去在添加 tags 和 categories 了;

部署 写作完成之后可以使用命令 hexo g生成静态页面.在后运行 hexo s查看效果; hexo d是部署到 coding 或者 github 上面,在站点配置文件中 deploy: type: git repo: https://git.coding.net/usename/usename.git #coding仓库地址 branch: coding-pages

*   你可以先到 coding 上面去申请一个帐号,然后行创建一个与你 coding 用户名相同的项目;
*   打开你刚才创建的项目,有一栏叫做 Page 服务,打开,然后选择部署;
*   hexo d是提交到你这个 repo的仓库,如果你没有配置 ssh,那么可能就提示您输入您的从coding 上的用户名和密码来验证;
*   如果宁蒗的输入,可以配置 ssh 公钥来进行配置点击查看配置 ssh 当您完成写作之后执行 hexo g & hexo s,过上一会就可以访问您的文章了;

绑定域名 如果您有自己的域名,你可以在在 dns 解析添加解析到 pages.coding.me;这样别人就可以通过域名访问您的博客了;