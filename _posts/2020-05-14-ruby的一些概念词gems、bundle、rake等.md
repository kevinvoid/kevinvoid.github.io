---
layout: post
title:  "ruby的一些概念词(gems、bundler、rake等)"
date:   2020-05-14 15:00:02 +0800
categories: 数据结构
---

### 简介
- 缘由: 使用Jekyll结合GithubPage搭建个人平台时使用到了相关ruby的知识点,在此记录下
- 本期相关概念词: ruby、gems、bundler、rake、rvm,后续再用到可能补充

### ruby
- 简介: A dynamic, open source programming language with a focus on simplicity and productivity. It has an elegant syntax that is natural to read and easy to write.
- 官网: [https://www.ruby-lang.org/en/](https://www.ruby-lang.org/en/)
- 命令: 
	- ruby -help
	- ruby -v
	- brew search ruby
	- 安装:brew install ruby@2.5

### gems:
- 简介: The RubyGems software allows you to easily download, install, and use ruby software packages on your system. The software package is called a “gem” which contains a packaged Ruby application or library.
- 官网: [https://guides.rubygems.org/](https://guides.rubygems.org/)
- 命令:
	- gem -help
	```
	kevinvoid.github.io git:(master) gem -h
	RubyGems is a sophisticated package manager for Ruby.  This is a
	basic help message containing pointers to more information.
	  Usage:
	    gem -h/--help
	    gem -v/--version
	    gem command [arguments...] [options...]
	  Examples:
	    gem install rake
	    gem list --local
	    gem build package.gemspec
	    gem help install
	  Further help:
	    gem help commands            list all 'gem' commands
	    gem help examples            show some examples of usage
	    gem help gem_dependencies    gem dependencies file guide
	    gem help platforms           gem platforms guide
	    gem help <COMMAND>           show help on COMMAND
	                                   (e.g. 'gem help install')
	    gem server                   present a web page at
	                                 http://localhost:8808/
	                                 with info about installed gems
	  Further information:
	    http://guides.rubygems.org
	```
	- gem install public_suffix --version 3.0.1
	- gem uninstall public_suffix
	- gem server
	```
	Server started at http://0.0.0.0:8808
	Server started at http://[::]:8808
	```
	You can access this documentation at http://localhost:8808
	- gem update
	- gem -v
	- 查看仓库: gem source -l
	```
	*** CURRENT SOURCES ***
	https://rubygems.org/
	```
	- gem 更改仓库地址: gem sources --add https://gems.ruby-china.com/ --remove https://rubygems.org/

### bundler
- 简介: Manage your Ruby application's gem dependencies;Bundler provides a consistent environment for Ruby projects by tracking and installing the exact gems and versions that are needed.
- 官网: [https://bundler.io/](https://bundler.io/)
- github地址: [https://github.com/rubygems/bundler](https://github.com/rubygems/bundler)
- 命令
	- gem install bundler
	- bundle init
	- bundle update
	- 配置镜像仓库: bundle config mirror.https://rubygems.org https://gems.ruby-china.com

### rake
- 简介: A make-like build utility for Ruby.
- 官网: [https://ruby.github.io/rake/](https://ruby.github.io/rake/)
- github地址: [https://github.com/ruby/rake](https://github.com/ruby/rake)
- 命令: 
	- gem install rake

### rvm
- 简介: Ruby Version Manager (RVM),RVM is a command-line tool which allows you to easily install, manage, and work with multiple ruby environments from interpreters to sets of gems.
- 官网: [https://rvm.io/](https://rvm.io/)
- 命令: 
	- 安装mpapis公钥: brew install gpg2
	- 安装mpapis公钥: gpg2 --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
	- Install RVM: \curl -sSL https://get.rvm.io | bash -s stable
	- For installing RVM with default Ruby and Rails in one command, run:
	\curl -sSL https://get.rvm.io | bash -s stable --rails
	- rvm -v
	- rvm list
	- rvm use 2.4.1
	- which ruby

### gemfile
- 简介: A format for describing gem dependencies for Ruby programs
- 参考链接: [https://bundler.io/man/gemfile.5.html](https://bundler.io/man/gemfile.5.html)

### rakefile (to be continued)


### 说明
- 参考文献: 
	- [https://henter.me/post/ruby-rvm-gem-rake-bundle-rails.html](https://henter.me/post/ruby-rvm-gem-rake-bundle-rails.html)
	- [https://www.rubyguides.com/2018/09/ruby-gems-gemfiles-bundler/](https://www.rubyguides.com/2018/09/ruby-gems-gemfiles-bundler/)
	- [https://cloud.tencent.com/developer/article/1596045](https://cloud.tencent.com/developer/article/1596045)
	- ruby、gems等官网


