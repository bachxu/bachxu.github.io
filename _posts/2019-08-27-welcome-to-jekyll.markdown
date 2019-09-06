---
layout: post
title:  "Welcome to Jekyll!"
date:   2019-08-27 10:59:37 +0800
categories: iTechnology
---
开篇主要想分享下如何搭建现有的博客系统，其实原先也通过 [Hexo](https://hexo.io/) 构建过一个博客系统，当时是需要两个库来维持，一个是源代码库，一个是生成库。相对来说现有的博客系统构建简单很多，还有个原因就是 GitHub 自从被微软收购后，感觉对开发者越来越友好。

现有的博客系统基于 [GitHub Pages](https://pages.github.com/) + [Jekyll](https://jekyllrb.com/)，下面主要讲解下如何在 macOS 搭建 Jekyll 环境。

### 命令行工具

首先我们需要安装命令行工具来保证本地的基础编译工作，在终端输入以下内容：

{% highlight shell %}
$ xcode-select --install
{% endhighlight %}

### 安装 Ruby 环境

虽然 macOS 系统自带有 Ruby 环境，但是可能存在 Ruby 版本太低，不符合安装 Jekyll 的版本要求，比如 macOS Mojave 10.14 自带的 Ruby 版本为 2.3.X，但是 Jekyll 需要 2.4.0 以上版本。因此我们需要安装 Ruby 环境，这边推荐的是通过 [rbenv](https://github.com/rbenv/rbenv) 来安装 Ruby。

rbenv 可以帮助我们管理多个版本的 Ruby，这对我们要在不同的项目使用不同的 Ruby 版本将会非常有用。

在安装 rbenv 之前，我们还需要安装另一个特别有用的工具——[Homebrew](https://brew.sh/)，它是一款用于 macOS 系统的包管理器，安装方法也很简单，在终端输入以下内容：

{% highlight shell %}
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
{% endhighlight %}

安装 Homebrew 的时候需要一个稳定的网络，安装成功后，通过 Homebrew 来安装 rbenv。如果在安装 Homebrew 的时候发现网络环境不是特别理想，可以尝试连接手机热点，亲测使用手机热点有效(移动 4G)，速度一下达到了兆。

{% highlight shell %}
# Install rbenv and ruby-build
$ brew install rbenv

# Setup rbenv integration to your shell
$ rbenv init

# Check your install
$ curl -fsSL https://github.com/rbenv/rbenv-installer/raw/master/bin/rbenv-doctor | bash
{% endhighlight %}

当执行 `rbenv init` 时出现报错，发现没有找到该执行命令，我们可以通过以下方式解决：

{% highlight shell %}
$ . /usr/local/Cellar/rbenv/1.1.2/bin/rbenv init
{% endhighlight %}

rbenv 成功安装和配置后，重启终端，这时我们可以安装相应版本的 Ruby 了。

{% highlight shell %}
$ rbenv install 2.6.3

# Set the global version
$ rbenv global 2.6.3

# Check the version
$ ruby -v
$ ruby 2.6.3p62 (2019-04-16 revision 67580)
{% endhighlight %}

### 安装 Jekyll

上面的步骤都成功走下来后，安装 Jekyll 就非常容易了，

{% highlight shell %}
$ sudo gem install -n /usr/local/bin/ jekyll
{% endhighlight %}

成功安装后即可搭建博客系统了，并通过相应的命令进行本地运行。

{% highlight shell %}
# Set the blog
$ jekyll new my-awesome-site

# Run the blog
$ cd my-awesome-site
$ bundle exec jekyll serve
# => Now browse to http://localhost:4000
{% endhighlight %}

### 总结

通过 GitHub Pages + Jekyll，我们只需要一个仓库即可管理所有东西，另外如果换了笔记本或者特殊情况，我们也可以尝试直接在仓库上直接撰写或者修改博客配置，个人觉得非常方便，希望大家也喜欢。

Follow your heart.
