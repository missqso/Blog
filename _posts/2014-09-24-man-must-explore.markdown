---
layout:     post
title:      "jekyll on github pages 部署记录"
subtitle:   "Problems look mighty small from 150 miles up"
date:       2015-03-27 00:59:00
author:     "nuaawao"
header-img: "img/post-bg-06.jpg"
---


#环境配置

### 安装 git

前往 https://windows.github.com/ 下载 git 
安装过程没有什么特别的

---

### 安装 Ruby

前往 http://rubyinstaller.org/downloads/ 下载 Ruby
安装中记得勾选“**Add Ruby executables to your PATH**”
Ruby 包含了 RubyGems ，RubyGems 是一个方便而强大的Ruby程序包管理器
打开一个命令提示行并输入以下命令来检测 Ruby 是否成功安装

    ruby -v

输出示例  

    ruby 2.0.0p451 (2014-02-24) [x64-mingw32]

---

### 安装 Ruby DevKit  

前往 http://rubyinstaller.org/downloads/ 下载 Ruby Devkit，注意对应 Ruby 的相关版本
下载得到的是一个压缩包，将其解压到`C:\RubyDevKit`
打开第一步中安装的 **Git Shell** 
跳转到 Ruby Devkit 的安装目录

```
cd C:\RubyDevKit
```

初始化

```
ruby dk.rb init
```

这将会在 RubyDevkit 的目录中生成一个`config.yml`，打开它，指定 Ruby 的安装位置



	---
	- C:/Ruby21-x64



我这里安装的是 Ruby 2.1.5 具体目录名字会不一样
开始安装Ruby DevKit ，将其绑定到 Ruby 

```
ruby dk.rb install
```

---

### 安装 bundler 

bundler 通过项目文件夹内的 gemfile 管理项目所需要的 gem ，在我们这个项目里，我们需要 jekyll / rouge / wdm 这三个 gem。

> 使用`bundle show [gemname]`可以查询 gem 在本地安装的位置

####[更换 Ruby Gems 镜像](https://ruby.taobao.org/)
由于国内的封锁，导致 bundler 安装失败，所以更换 Ruby Gems 镜像
> 为什么有这一步
>> 由于国内网络原因（你懂的），导致 rubygems.org 存放在 Amazon S3 上面的资源文件间歇性连接失败。所以你会与遇到 gem
>> install rack 或 bundle install 的时候半天没有响应，具体可以用 gem install rails -V
>> 来查看执行过程。

>如何更换 ？
>```
>$ gem sources --remove https://rubygems.org/
>$ gem sources -a https://ruby.taobao.org/
>$ gem sources -l
>*** CURRENT SOURCES ***

>https://ruby.taobao.org
> # 请确保只有 ruby.taobao.org
>```
#### 正式安装 bundler  
执行安装命令

    gem install bundler


---

以下三个 gem 都是可以在 Gemfile 中指定然后通过 `bundle` 安装的 gem ，先后不同而已，结果一样，推荐跳过，在之后的 Gemfile 文件中指定
###安装 jekyll(当前可选)
jekyll 是作为一个 gem 存在的，所以只需要通过 gem 执行命令即可

    gem install jekyll

一次装了 31 个 gem ......

---

### 安装 Rouge  （当前可选）

无论是你用 markdown 或者是 html ，jekyll 都可以在你的页面中插入漂亮的代码块。
默认情况下， jekyll 自带 pygments.rb ，这是一个基于 python 的语法高亮显示器，要在 windows 上使用的话，还得安装 python ，会比较麻烦。
另一个不错的选择是基于 Ruby 的 Rouge ，它更快而且更加易于安装，只是支持的语言没有 pygments 那么多，具体选择可以考虑一下。

    gem install rouge

在之后的 jekyll 所生成的头文件 `_config.yml`里，将 rouge 设置为代码高亮显示器

    highlighter: rouge


---

### 安装 wdm （当前可选）  

jekyll 有一个内置的自动更新功能，监控源文件夹的变化，然后更新你的网站。
从 jekyll 2.4.0 开始，`jekyll server`命令默认启用这项设定。或者使用`jekyll build --watch`去手动启用它。
wdm 全称 **Windows Directory Monitor** ，使用它似乎可以避免轮询（rolling，一种低效率的监控变化的方式）

    gem install wdm

也可以在之后的 gemfile 文件里面指定 wdm ，然后执行`bundle`命令，后话。

---

# 创建 jekyll 网站  

###生成 Gemfile 文件

这个时候所有的环境已经配置好了，可以开始建立 jekyll 网站了
```
cd e:\

# 建立 blog 文件夹
mkdir blog
cd blog
#创建 Gemfile 空文件，bundler 使用这个文件管理当前项目中所使用的 gem 及其版本号
touch Gemfile 
```    

然后在文本编辑器中打开 Gemfile

    notepad Gemfile

输入以下文本，Gemfile 内带上明确的版本号是个好习惯

    source 'https://ruby.taobao.org/'
	gem 'jekyll' , '2.5.3'
	gem 'rouge' , '1.8.0'
	gem 'wdm', '~> 0.1.0' if Gem.win_platform?
	# 为了把 pygments 给锁在目前的版本，因为不需要它来做高亮
	gem 'pygments.rb','0.6.3'

在命令行执行

    bundle

这个时候会按照在 Gemfile 中指定的 gem 来寻找所需要的 gem 以及它们的关联 gem ，并下载所缺的 gem，完成之后可以使用`gem list`来检查是否安装完毕

---
### 开始创建 jekyll 网站
	
	# 跳转到项目目录
    cd e:\WebsiteName
    
    # 使用 Gemfile 中指定的 jekyll 版本在当前文件夹强制创建网站
    # bundle exec 的作用是使用 Gemfile 中指定的 jekyll 版本
    # . 的意思是当前目录，-f 是强制创建，因为之前已经生成了一个 Gemfile 文件在此目录中
    
    bundle exec jekyll new . -f


这个时候在 WebsiteName 目录中生成了网站的根目录。因为我们决定使用 rouge 来作为语法高亮显示器，所以要在 `_config.yml` 中指定 rouge 作为语法高亮显示器

    highlighter: rouge

然后把网站部署到本地，注意这里也需要 `bundle exec` 

    bundle exec jekyll server

这个时候，就可以访问 `localhost:4000` 来访问自己的网站了

---
### 通过 rake 命令新建文章
> 通过 `gem list rake` 来检查自己是否安装了 rake ，若尚未安装，使用 `gem install rake` 安装

rake 任务在一个特殊的文件中定义，名为 Rakefile 。下面，在网站根目录中创建 Rakefile 文件

    cd e:/blog
    touch Rakefile

在文本编辑器中打开，写入下述内容，详情可以参见[此处](https://github.com/AndorChen/jekyll-book/blob/master/Rakefile)：

    require 'time'
    # Usage: rake post title="A Title" [date="2014-04-14"]
	desc "Create a new post"
	task:postdo
		 unlessFileTest.directory?('./_posts')
			 abort("rake aborted: '_posts' directory not found.")
		 end
		 title = ENV ["title"] || "new-post"
		 slug=title.downcase.strip.gsub(' ','-').gsub(/[^\w-]/,'')
		 begin
	         date=(ENV['date']?Time.parse(ENV['date']):Time.now)
			      .strftime('%Y-%m-%d')
		 rescue Exception=>e
			 puts"Error: date format must be YYYY-MM-DD!"
			 exit-1
		 end
		 filename=File.join('.','_posts',"#{date}-#{slug}.md")
		 if File.exist?(filename)
			 abort("rake aborted:#{filename}already exists.")
		 end
		 
		 puts"Creating new post:#{filename}"
		 open(filename,'w')do|post|
			 post.puts"---"
			 post.puts"layout: post"
			 post.puts"title:\"#{title.gsub(/-/,' ')}\""
			 post.puts"date:#{date}"
			 post.puts"categories:"
			 post.puts"---"
		 end
	end

Rakefile 使用 Ruby 语言编写。上述代码定义了一个 rake 任务，名为 post。这个任务的作用是，在 `_post` 文件夹中新建一个文件，并写入一些默认内容，我们来看一下如何执行这个任务：

    ~/blog $ rake post title="Hello, again"
    Creating new post: ./_posts/2015-03-25-hello-again.md

date 是可选项
		
    ~/blog $ rake post title="Hello, there" date="2015-03-26"
    Creating new post: ./_posts/2015-03-26-hello-there.md

 第二个单词前最好加空格，这样就会中文件名内生成连字符，rake 会自动把 title 里除了文字之外的符号去除，把空格转换成连字符，如果没有加上空格：

    ~/blog $ rake post title="hello,again"
    Creating new post: ./_posts/2015-03-25-helloagain.md

`2015-03-25-helloagain.md` 这样的文件名就不具备可读性

	
### 通过 rake 命令新建草稿文章
在网站根目录新建 `_drafts` 文件夹

    ~blog/ $ mkdir _drafts

把以下代码添加到 Rakefile 文件中：

    desc"Create a new draft"
		task:draft do
		  unless FileTest.directory?('./_drafts')
		    abort("rake aborted: '_drafts' directory not found.")
		  end
		  title=ENV["title"]||"new-post"
		  slug=title.downcase.strip.gsub(' ','-').gsub(/[^\w-]/,'')
		  filename=File.join('.','_drafts',"#{slug}.md")
		  if File.exist?(filename)
		    abort("rake aborted:#{filename}already exists.")
		  end
		  
		  puts"Creating new draft:#{filename}"
		  open(filename,'w')do|post|
		    post.puts"---"
		    post.puts"layout: post"
		    post.puts"title:\"#{title.gsub(/-/,' ')}\""
		    post.puts"date:"
		    post.puts"categories:"
		    post.puts"---"
		  end
		end
新建 draft 的用法和 post 的用法类似，但是不用指定 date 参数

    ~/blog $ rake draft title="first draft"


### 进一步配置 config 文件
#### 更改固定连接设置
打开 _config.yml ，添加

    permalink: '/:year/:month/:title'

#### 更改时区设置

    timezone: '+0800'

#### 更改 kramdown 语法高亮设置

    kramdown:
		  input: GFM
		  syntax_highlighter: rouge



