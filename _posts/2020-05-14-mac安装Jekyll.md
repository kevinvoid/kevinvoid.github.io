---
layout: post
title:  "mac安装Jekyll"
date:   2020-05-14 11:08:02 +0800
categories: 数据结构
---


### 一、Jekyll简介
+ Transform your plain text into static websites and blogs.
+ 官网: [https://jekyllrb.com/](https://jekyllrb.com/)

### 二、Jekyll安装及启动步骤
- 说明: 安装Jekyll之前本地环境要安装ruby,而且还有版本限制.mac自带了ruby,通过命令:ruby -v查看ruby版本  
- 步骤:  
	- 1.安装: gem install bundler jekyll  
	- 2.配置jekyll环境变量  
		+ 1> 将/usr/local/lib/ruby/gems/2.5.0/gems/jekyll-4.0.1/exe加入到用户的环境变量中  
		+ 2> source ~/.zshrc 或者 source ~/.bash_profile  
	+ 3.查看jekyll版本
		+ jeyll -v  
	+ 4.创建简单的website  
		+ 1> jekyll new my-site  
		+ 2> cd my-site
		+ 3> 启动: jekyll serve / bundle exec jekyll serve  
		启动日志:  
	```
	➜  kevinvoid.github.io git:(master) ✗ jekyll serve
	Configuration file: /Users/apple/tool/jekyll/kevinvoid.github.io/_config.yml
	            Source: /Users/apple/tool/jekyll/kevinvoid.github.io
	       Destination: /Users/apple/tool/jekyll/kevinvoid.github.io/_site
	 Incremental build: disabled. Enable with --incremental
	      Generating...
	       Jekyll Feed: Generating feed for posts
	                    done in 0.781 seconds.
	 Auto-regeneration: enabled for '/Users/apple/tool/jekyll/kevinvoid.github.io'
	    Server address: http://127.0.0.1:4000/
	  Server running... press ctrl-c to stop.
	```
		+ 4> 访问: [https://jekyllrb.com/](http://localhost:4000) 访问

### 三、说明
+ jekyll serve启动的过程出现“You have already activated public_suffix 4.0.5, but your Gemfile requires public_suffix 4.0.3. Prepending `bundle exec` to your command may solve this. (Gem::LoadError)“错误提示,可通过gem命令安装或卸载.  
	+ 安装: gem install public_suffix --version 3.0.1 
	+ 卸载: gem uninstall public_suffix 
