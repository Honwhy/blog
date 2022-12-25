---
layout: posts
classes: wide
category: blog jekyll
comments: true
title: 重新组织一下个人网站
tags: collect
---
* content
{:toc}

## 概述
重新组织个人主页，将由Blog和Projects两个部分组成，同时在Blog子站中加入Links友链。

## 缘起
由于前同事邀请我互相添加友链，考虑我的主页是简约风格的，一时半会不知道添加到哪里合适。综合考虑最近捣鼓了【深圳图书馆浏览器插件】这个作品，那么自然想到了将主站划分为博客和项目两部分，博客可以继续添加博客日志、添加友链，项目部分可以集结一下之前的内容。

# 分析
首先honwhy.wang这个域名是个人所有的顶级域名，理论是可以添加子域名的，比如blog.honwhy.wang域名，但是必须看github pages是否支持。通过github文档了解到，个人账号只可以创建一个`<username>`、一个`<organization>`及多个`<username>/<repository>`这样的page。`<organization>`已经用于了朴素AI小组了，那么只能采用第三方方案了。

# 踩坑
再次使用Jekyll方式构建主页和博客还是遇到了不少坑的。下面只记录成功执行的方式，略去错误的步骤
- 使用window 11 sub system linux, openSUSE Tumbleweed，安装ruby及Jekyll等

    道听途说opensuse的风滚草可以滚动更新，而且比较其他发行版更加稳定不怕滚坏。所以就安装了它。首先升级wsl2就可以在Microsoft Store搜索`opensuse`进行安装。更新系统命令`sudo zypper dup`。在这个系统上安装jekyll失败未找到解决方法时，一度想改换发行版，搜索发现有一款基于Debian的发行版做到了滚动更新，专门适配了wsl的，不过是收费的，叫`pengwin`。
    - 安装ruby及ruby-devel
    - 安装jekyll
    遇到问题的话，请参考Jekyll官方文档，https://jekyllrb.com/docs/
    ```bash
    # 安装基础依赖
    sudo zypper install -t pattern devel_ruby devel_C_C++
    sudo zypper install ruby-devel
    # 用普通用户权限使用gems
    echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
    echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
    echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
    source ~/.bashrc
    # 将jekyll加入到PATH变量
    echo 'export PATH=/usr/lib64/ruby/gems/3.1.0/gems/jekyll-4.3.1/exe:$PATH' >> ~/.bashrc
    # 接下来就不建议安装官方文档新建一个blog了

    ```
- 使用remote theme形式使用minimal-mistakes主题

    省略中间的踩坑过程。为了使用默认主题之外的主题，需要使用remote-theme，最好的方式是通过remote-theme脚手架生成一个新的项目。参考 https://github.com/mmistakes/minimal-mistakes 的建议，点击[Minimal Mistakes remote theme starter](https://github.com/mmistakes/mm-github-pages-starter/generate)就可以生成一个博客了。

    将旧的博客内容迁移到了这个新的博客，调整layout，相关文档：https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/

    由于github经常丢包，我在国内搞了一个镜像，https://gitcode.net/weixin_57675453/minimal-mistakes

    这个theme可能无法直接用的，相关调整
    ```bash
    # 在这个位置加入github-pages和jekyll-remote-theme两个plugin
    group :jekyll_plugins do
        gem "jekyll-paginate"
        gem "jekyll-sitemap"
        gem "jekyll-gist"
        gem "jekyll-feed"
        gem "jemoji"
        gem "jekyll-include-cache"
        gem "jekyll-algolia"
        gem "github-pages"
        gem "jekyll-remote-theme"
    end
    ```
- 配置github page
    
    配置完github page，主要本地推送代码上去，github就会自动部署了，具体可以看actions面板。
    
    本地启动就是
    ```bash
    bundle install
    bundle exec jekyll serve --verbose
    ```
- 调整主站

    这个子站最终的路径是honwhy.wang/blog，接下来是调整主站。将主站的内容清空，新建一个单独的页面。参考splash模板写好一个画栏模式的展示页，注意permalink应该是/。
    ```bash
        ---
        title: "Home"
        layout: splash
        permalink: /
        feature_row:
        - image_path: assets/images/blog.jpg
            alt: ""
            title: "Blog"
            image_caption: "Blog about java and others"
            url: "/blog"
        - image_path: assets/images/projects.jpg
            alt: ""
            title: "Projects"
            image_caption: "Projects like java library and chrome extensions"
            url: "/projects"
        ---

        {% include feature_row %}
    ```