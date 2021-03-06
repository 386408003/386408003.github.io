---
layout: post
title: 使用Jekyll在本地测试GitHub Pages网站
categories: GitHub
description: 使用Jekyll在本地测试GitHub Pages网站
keywords: Jekyll, GitHub Pages, ruby, gems
---

# 使用Jekyll在本地测试 GitHub Pages 网站

## 一、安装 [Jeklly](https://jekyllrb.com/docs/installation/)。

**注意：本人系统（Windows10 64位）**

建议使用 Bundler 安装和运行 Jekyll。Bundler 管理 Ruby gem 依赖项，减少 Jekyll 构建错误，并防止与环境相关的错误。要安装 Bundler，请执行以下操作：

### 1. 安装 Ruby。

Windows 系统下[ Ruby 安装包下载地址](https://rubyinstaller.org/downloads/)

检查是否安装成功：

```sh
ruby -v
```

其他系统安装详情请参见“[安装 Ruby](https://www.ruby-lang.org/en/documentation/installation/)  ”。

### 2. 安装 Bundler。

```sh
gem install bundler
```

详情请参见“[安装 Bundler](https://bundler.io/) ”。

### 3. 官方对 Gems、Gemfile、Bundler 的简单讲解

官方对相关术语解释说明[点击此处查看](https://jekyllrb.com/docs/ruby-101/#gems)

一个 Gemfile 的简单示例：

```ruby
source "https://rubygems.org"

gem "jekyll"

group :jekyll_plugins do
  gem "jekyll-feed"
  gem "jekyll-seo-tag"
end
```



## 二、创建一个 Jekyll 网站。

### 1. 只需几行命令，赶紧来体验吧！

1. Jekyll Helloworld 程序可参考 [Jekyll 官网](https://www.jekyll.com.cn/)。

```sh
gem install bundler jekyll
jekyll new my-awesome-site
cd my-awesome-site
bundle exec jekyll serve

# => 打开浏览器 http://localhost:4000
```

2. 在本地运行您的 Jekyll 网站，参考 [GitHub Docs](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/testing-your-github-pages-site-locally-with-jekyll) 官网。

```sh
bundle install
bundle exec jekyll serve

> Configuration file: /Users/octocat/my-site/_config.yml
>            Source: /Users/octocat/my-site
>       Destination: /Users/octocat/my-site/_site
> Incremental build: disabled. Enable with --incremental
>      Generating...
>                    done in 0.309 seconds.
> Auto-regeneration: enabled for '/Users/octocat/my-site'
> Configuration file: /Users/octocat/my-site/_config.yml
>    Server address: http://127.0.0.1:4000/
>  Server running... press ctrl-c to stop.
```
```sh
# 调试过程中遇到如下报错：
# 报错：No source of timezone data could be found
# 通过百度解决了此问题，由于第一次接触这些技术，暂时无经历了解，导致原因不明。
# Gemfile配置添加如下配置即可：
gem 'tzinfo-data', platforms: [:mingw, :mswin, :x64_mingw]

# 然后执行：
bundle update
bundle exec jekyll serve
```



## 三、更新 GitHub Pages gem 。

按照官方说法，Jekyll 是一个活跃的开源项目，经常更新。如果您计算机上的 `github-pages` gem 与 GitHub Pages 服务器上的 gem 相比已经过期了，则您的站点在本地构建时可能与在 GitHub 上发布的站点看起来有所不同。为避免这种情况，请定期更新 `github-pages` 计算机上的 gem 。

- 如果您安装了 Bundler

  ```sh
  bundle update github-pages
  ```

- 如果您没有安装 Bundler

  ```sh
  gem update github-pages
  ```

  