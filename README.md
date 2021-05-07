# 小屁孩的梦

我的个人博客：<http://blog.hkyzf.top>，欢迎 Star 和 Fork。

## 1. Fork 指南

Fork 本项目之后，还需要做一些事情才能让你的页面「正确」跑起来。

1. 正确设置项目名称与分支。

   按照 GitHub Pages 的规定，名称为 `username.github.io` 的项目的 master 分支，或者其它名称的项目的 gh-pages 分支可以自动生成 GitHub Pages 页面。

2. 修改域名。

   如果你需要绑定自己的域名，那么修改 CNAME 文件的内容；如果不需要绑定自己的域名，那么删掉 CNAME 文件。

3. 修改配置。

   网站的配置基本都集中在 \_config.yml 文件中，将其中与个人信息相关的部分替换成你自己的，比如网站的 url、title、subtitle 和第三方评论模块的配置等。

   **评论模块：** 目前支持 disqus、gitment、gitalk 和 valine，选用其中一种就可以了。它们各自的配置指南链接在 \_config.yml 文件的 Comments 一节里都贴出来了。

   **注意：** 如果使用 disqus，因为 disqus 处理用户名与域名白名单的策略存在缺陷，请一定将 disqus.username 修改成你自己的，否则请将该字段留空。

4. 删除我的文章与图片。

   如下文件夹中除了 template.md 文件外，都可以全部删除，然后添加你自己的内容。

   * \_posts 文件夹中是我已发布的博客文章。
   * \_drafts 文件夹中是我尚未发布的博客文章。
   * \_wiki 文件夹中是我已发布的 wiki 页面。
   * images 文件夹中是我的文章和页面里使用的图片。

5. 修改「关于」页面。

   pages/about.md 文件内容对应网站的「关于」页面，里面的内容多为个人相关，将它们替换成你自己的信息，包括 \_data 目录下的 skills.yml 和 social.yml 文件里的数据。

## 2. 温馨提示

1. 在本地预览博客效果可以参考 [Setting up your Pages site locally with Jekyll][2]。
2. 排版建议遵照一定的规范，推荐 [中文文案排版指北（简体中文版）](https://github.com/mzlogin/chinese-copywriting-guidelines)。
3. [本博客模板常见问题 Q & A](https://mazhuang.org/2020/05/03/blog-template-qna/)。

## 3. 目录结构说明

- _data    数据
  - links.yml    链接→类似友情链接
  - skills.yml    关于→技能
  - social.yml    关于→联系
- _drafts    尚未发布的博客文章
  - template.md    尚未发布的博客文章模板
- _includes 包含组件
  - article-footer.html    文章底部关注信息
  - comments.html    评论模块
  - footer.html    页面头信息
  - header.html    页面尾信息
  - sidebar-categories-nav.html    分类标签下右侧博客分类组件
  - sidebar-popular-repo.html    首页右侧受欢迎的仓库
  - sidebar-post-nav.html    暂时不知道什么作用
  - sidebar-search.html    右侧搜索框
  - sns-share.html    暂时不知道什么作用
- _layouts    页面布局文件夹
  - categories.html    分类页面布局
  - default.html    默认布局
  - page.html    维基、链接、关于页面布局
  - post.html    文章内容页面布局
  - wiki.html    暂时不知道什么作用
- _posts    已发布到分类模块博客文章
  - template.md    已发布的博客文章模板
- _wiki    已发布的 wiki(维基) 页面
  - template.md    已发布的 wiki 页面模板
- assets    资源文件夹 css/js/images
- images    文章和页面里使用的图片
- pages    页面
  - 404.md    404页面
  - about.md    关于页面
  - categories.md    分类页面
  - links.md    链接页面
  - open-source.md    暂时不知道什么作用
  - wiki.md    维基页面
- .gitignore    定义忽略文件推送的规则
- _config.yml    主配置文件
- CNAME    自定义域名使用，不需要绑定自己的域名，可以删掉此文件
- favicon.ico    网站标签页上展示的小图标
- Gemfile    类似maven管理依赖用
- Gemfile.lock    Bundler记录已经安装了的版本的地方，换机后还会通过此文件安装相同版本
- index.html    网站首页
- LICENSE    许可协议
- README.md    当前正在阅读的本文件

## 4. 致谢

本博客外观基于 [https://mazhuang.org](https://mazhuang.org/) 修改，本文也有部分链接直接使用 码志 文章内容，感谢！
