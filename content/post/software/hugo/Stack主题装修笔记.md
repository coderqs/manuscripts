+++
author = "清松"
title = "[Hugo] Stack 主题装修笔记"
date = "2022-02-23"
description = ""
tags = [
    "Hugo",
    "Blog",
]
categories = [
    "software",
]
series = ["Hugo Guide"]
aliases = [""]
image = ""
+++

## 在网页的页脚增加 cc 协议的图标
1. 先在 [cc 官网把对应的 svg 图标下载下来](https://creativecommons.org/about/downloads/)，将其放在 `assets\icons` 下，我是在这个目录下新建了目录 `cc-icons` 并放在里。
2. 在文件 `layouts/partials/footer/footer.html` 中 `<section class="powerby">` 的部分添加下面的代码
``` html
{{ if .Site.Params.footer.ccLicenseIcon }}
    <div class="cc-license-icons">
        <a rel="license" href="https://creativecommons.org/licenses/by-nc-sa/4.0/deed.zh" target="_blank" title="Licensed Under CC BY-NC-SA 4.0">
            <i class="cc-icon-cc"> {{ partial "helper/icon" "cc-icons/cc" }} </i>
            <i class="cc-icon-by"> {{ partial "helper/icon" "cc-icons/by" }} </i>
            <i class="cc-icon-nc"> {{ partial "helper/icon" "cc-icons/nc" }} </i>
            <i class="cc-icon-sa"> {{ partial "helper/icon" "cc-icons/sa" }} </i>
        </a>
    </div>
{{ end }}
```
3. 在 `assets/scss/partials/footer.scss` 中 `.powerby` 的后面添加下面的代码
``` scss
.cc-license-icons {
    svg {
         width: 20px;
         height: 20px;
         color: var(--body-text-color);
    }
}
```
4. 在 `config.yaml` 中 `footer` 的部分添加 `ccLicenseIcon: true` 配置项
``` yaml
footer:
    since:
    customText:
    ccLicenseIcon: true
```

## 标题与图标对齐
将右侧小部件的标题与图标移到一行并对齐
### 修改样式
1. 在文件 `assets/scss/partials/widgets.scss` 中 `widget-icon` 的下面添加下面的代码
```
.widget-icon-title {
    svg {
        width: 32px;
        height: 32px;
        //stroke-width: 1.6;
        color: var(--body-text-color);
    }

    display: flex;
    align-items: center;
}
```
2. 在文件 `assets/scss/style.scss` 的最后添加
```
.has-icon-section-title {
    text-transform: uppercase;
    margin-top: 0;
    margin-bottom: 0;
    display: block;
    font-size: 2.25rem;
    font-weight: bold;
    color: var(--body-text-color);

    a {
        color: var(--body-text-color);
    }
}
```
### 修改标题和图标
修改 archives、tag-cloud、文章目录的标题和图标
1. archives 的相关修改在文件 `layouts/_default/single.html` 中。将下面的部分
```
{{ define "right-sidebar" }}
    {{ if (.Scratch.Get "hasTOC") }}
        <aside class="sidebar right-sidebar sticky">
            <section class="widget archives">
                <div class="widget-icon-title">
                    {{ partial "helper/icon" "hash" }}
                </div>
                    <h2 class="widget-title section-title">{{ T "article.tableOfContents" }}</h2>
                
                <div class="widget--toc">
                    {{ .TableOfContents }}
                </div>
            </section>
        </aside>
    {{ end }}
{{ end }}
```
修改成
```
{{ define "right-sidebar" }}
    {{ if (.Scratch.Get "hasTOC") }}
        <aside class="sidebar right-sidebar sticky">
            <section class="widget archives">
                <div class="widget-icon-title">
                    {{ partial "helper/icon" "hash" }} &nbsp;&nbsp;
                    <h2 class="widget-title section-title">{{ T "article.tableOfContents" }}</h2>
                </div>
                
                <div class="widget--toc">
                    {{ .TableOfContents }}
                </div>
            </section>
        </aside>
    {{ end }}
{{ end }}
```
为了显得不那么紧凑在中间加了两个空格

2. tag-cloud 和文章目录的修改方式和这个一致，分别在文件 `layouts/partials/widget/tag-cloud.html`(大概第3行) 和 `layouts/partials/widget/archives.html`(大概第5行) 中。

## 左侧站点信息居中
### 头像、站点名称、描述
修改文件 `assets/scss/partials/sidebar.scss`
#### 头像
``` scss
.site-avatar {
    position: relative;
    margin: 0;
    margin-left: auto; //站点头像居中
    margin-right: auto; //站点头像居中
    width: var(--sidebar-avatar-size);
    height: var(--sidebar-avatar-size);

    margin-bottom: var(--sidebar-element-separation);

    .site-logo {
        width: 100%;
        height: 100%;
        border-radius: 100%;
        box-shadow: var(--shadow-l1);
    }
```

#### 站点名称
```

.site-name {
    color: var(--accent-color);
    text-align: center; //站点名称居中
    font-size: 1.8rem;

    @include respond(2xl) {
        font-size: 2rem;
    }
}
```
#### 站点描述
```
.site-description { 
    color: var(--body-text-color);
    font-weight: normal;
    text-align: center; //站点描述居中
    font-size: 1.6rem;

    @include respond(2xl) {
        font-size: 1.8rem;
    }
}
```

### 社交链接图标
修改文件 `assets/scss/partials/menu.scss`
``` scss
.social-menu {
    list-style: none;
    padding: 0%;
    display: flex;
    flex-direction: row;
    gap: 10px;
    justify-content: center;
    
    svg {
        width: 24px;
        height: 24px;
        stroke: var(--body-text-color);
        stroke-width: 1.33;
    }
}
```

## 增加文章字数统计
这个根据主题的阅读时间修改，所以就嵌合到一起了
在文件 `layouts/partials/article/components/details.html` 中 `{{ if .Site.Params.article.readingTime }}` 部分增加
```
<div>
    {{ partial "helper/icon" "brush" }}
    <time class="article-words">
        {{ T "article.wordCount" .WordCount }}
    </time>
</div>
```
在文件 `i18n/zh-CN.yaml` 中的 `article` 的部分里增加
```
    wordCount:
        other: "{{ .Count }} 字"
```

## 增加站点总字数、上线时间统计
修改都在文件 `layouts\partials\footer\footer.html` 中

### 上线时间
在 `<section class="copyright">` 部分增加
```
<section class="copyright">
    &copy;
    {{ if and (.Site.Params.footer.since) (ne .Site.Params.footer.since (int (now.Format "2006"))) }}
        {{ .Site.Params.footer.since }} -
    {{ end }}
    {{ now.Format "2006" }} {{ .Site.Title }} <br />
    已运行 <i class="fas fa-bell"></i> <a id="days">0</a> 天
</section>
```
在 `<footer class="site-footer">` 的最后添加
```
<script>
    var s1 = '2022-01-01';
    s1 = new Date(s1.replace(/-/g, "/"));
    s2 = new Date();
    var days = s2.getTime() - s1.getTime();
    var number_of_days = parseInt(days / (1000 * 60 * 60 * 24));
    document.getElementById('days').innerHTML = number_of_days;
</script>
```

### 站点总字数
在上线时间的后面添加就行
```
已运行 <i class="fas fa-bell"></i> <a id="days">0</a> 天
    {{$scratch := newScratch}}
    {{ range (where .Site.Pages "Kind" "page" )}}
        {{$scratch.Add "total" .WordCount}}
    {{ end }}

    ⌨ {{ div ($scratch.Get "total") 1000.0 | lang.FormatNumber 2 }}k 字 🧠 {{ len (where .Site.RegularPages "Section" "post") }} 篇文章
</script>
```

### 优化 - 可通过配置文件设置
1. `layouts\partials\footer\footer.html` 修改的部分
```
{{- $ThemeVersion := "3.8.0" -}}
<footer class="site-footer">
    <section class="copyright">
        &copy;
        {{ if and (.Site.Params.footer.since) (ne .Site.Params.footer.since (int (now.Format "2006"))) }}
            {{ .Site.Params.footer.since }} -
        {{ end }}
        {{ now.Format "2006" }} {{ .Site.Title }}
    
        {{ if .Site.Params.footer.RunDays.enabled }}
            <br />
            {{ T "footer.runDays" }} <i class="fas fa-bell"></i> <a id="days">0</a> {{ T "footer.runDays1" }}
        {{ end }}
    
        {{ if .Site.Params.footer.articleTotalStatistics.enabled }}
            &nbsp;&nbsp;
            {{$scratch := newScratch}}
            {{ range (where .Site.Pages "Kind" "page" )}}
                {{$scratch.Add "total" .WordCount}}
            {{ end }}
            
            {{ .Site.Params.footer.articleTotalStatistics.totalArticleWordEmoji }}&nbsp;
            {{ div ($scratch.Get "total") 1000.0 | lang.FormatNumber 2 }}k&nbsp;
            {{ T "footer.totalArticleWord" }}
            &nbsp;&nbsp;
            {{ .Site.Params.footer.articleTotalStatistics.totalArticleEmoji }}&nbsp;
            {{ len (where .Site.RegularPages "Section" "post") }}&nbsp;
            {{ T "footer.totalArticle" }}
        {{ end }}
    </section>
    
    ...
    
    {{ if .Site.Params.footer.RunDays.enabled }}
        <script>
            var s1 = "{{ .Site.Params.footer.RunDays.launchDate }}";
            s1 = new Date(s1.replace(/-/g, "/"));
            s2 = new Date();
            var days = s2.getTime() - s1.getTime();
            var number_of_days = parseInt(days / (1000 * 60 * 60 * 24));
            document.getElementById('days').innerHTML = number_of_days;
        </script>
    {{ end }}
</footer>
```

2. `i18n/zh-CN.yaml` 中添加
```
footer:
    runDays:
        other: "已运行"
    runDays1:
        other: "天"

    totalArticleWord:
        other: "字"

    totalArticle:
        other: "篇文章"
```
3. `config.yaml` 中添加
```
footer
    RunDays:
        enabled: true
        launchDate: "2022-01-01"
    articleTotalStatistics: 
        enabled: true
        totalArticleWordEmoji: ⌨
        totalArticleEmoji: 🧠
```

## 屏蔽 Google Analytics 本地计数
修改 `layouts/partials/head/head.html` 
```
{{- if not .Site.IsServer }}
    {{- template "_internal/google_analytics.html" . -}}
{{- end }}
```

## 增加分类小部件
增加文件 `layouts/partials/widget/categories.html`
```
{{- $query := first 1 (where .Site.Pages "Layout" "==" "archives") -}}
{{- if $query -}}
    {{- $archivesPage := index $query 0 -}}
    <section class="widget categories">
        <div class="widget-icon-title">
            {{ partial "helper/icon" "stack-2" }} &nbsp;&nbsp; 
            <h2 class="widget-title has-icon-section-title">{{ T "widget.categories.title" }}</h2>
        </div> 
    <div class="widget-categories--list">
<div class="widget">
    <h3 class="widget-title"> {{ T "widget_categories" }}</h3>
    <div class="widget-body">
        <div class="category-list">
            {{- range $name, $taxonomy := $.Site.Taxonomies.categories }}
            {{- with $.Site.GetPage (printf "/categories/%s" $name) }}
            <div class="category-list-item">
                <a href="{{ .Permalink }}" class="category-list-link">{{ $name }}<span class="category-list-count">{{ $taxonomy.Count}}</span></a>
            </div>
            {{- end }}
            {{- end }}
        </div>
    </div>
</div>
    </div>
    </section>
{{- else -}}
    {{- warnf "Archives page not found. Create a page with layout: archives." -}}
{{- end -}}

```
在文件 `assets/scss/partials/widgets.scss` 中增加
```
/* Categories widget */
.widget.categories {
    .widget-categories--list {
        border-radius: var(--card-border-radius);
        box-shadow: var(--shadow-l1);
        background-color: var(--card-background);
    }

    .category-list-item {
        &:not(:last-of-type) {
            border-bottom: 1.4px solid var(--card-separator-color);
        }

        a {
            font-size: 1.4rem;
            padding: 18px 25px;
            display: flex;
            justify-content: space-between;
            color: var(--card-text-color-main);
        }

        span.category-list-count {
            color: var(--card-text-color-tertiary);
        }
    }
}
```
在文件 `i18n/zh-CN.yaml` 中 `widget` 的部分增加
```
categories:
    title:
        other: 分类
```

## 增加是否需要文章头图的设置
将文件 `layouts\partials\article\components\header.html` 中 
```
{{ if $image.exists }}
```
(我这里是第 3 行) 改为 
```
{{ if and (.Site.Params.Article.showHeaderImage) ($image.exists) }}
```
再在文件 `config.yaml` 中 `article` 部分添加
```
article:
    math: false
    toc: true
    readingTime: true
    showHeaderImage: true
```

## 文章显示左右侧边栏
未完待续

## 修改友链的样式
### 修改样式

### 改为双栏
在文件 `assets/scss/custom.scss` 中添加
```
@media (min-width: 1024px) {
    .article-list--compact.links {
        display: grid;
        grid-template-columns: 1fr 1fr;
        background: none;
        box-shadow: none;

        article {
            background: var(--card-background);
            border: none;
            box-shadow: var(--shadow-l2);
            margin-bottom: 8px;
            border-radius: 10px;

            &:nth-child(odd) {
                margin-right: 8px;
            }
        }
    }
}
```


## 修改分类的颜色
在 `content\categories` 创建与分类同名的文件夹，并且在文件夹中创建 `_index.md` 文件并添加下面的内容
```
title: "操作系统" //分类名称
description: "简介" // 分类简介，不需要可以删了
image: "categories.png" // 分类题图，也可以删了
style:
    background: "#80aba9" //分类标签底色
    color: "#fff"
---

```

## 参考资料
[Hugo | 看中 Stack 主题的归档功能，搬家并做修改](https://mantyke.icu/2021/f9f0ec87/)  
[Hugo | 另一篇 Stack 主题装修记录](https://mantyke.icu/2021/a08f1963/)  
[Hugo | 第三篇 Stack 主题装修记录，堂堂再临！](https://mantyke.icu/2022/stack-theme-furnish03/)  
[hugo主题stack](https://yinhe.co/archives/20210401_hugo_theme_stack.html#hugo)  
[Hugo Stack 主题配置与使用](https://bore.vip/archives/3bf3725e/)  
[博客装修：删删改改大胆行事（2022-01-27 Update）](https://gregueria.vercel.app/posts/decoration/)  
[Hugo Stack主題修改記錄 (瘦身篇)](https://www.bigs3.com/article/modify-hugo-theme-stack-one/)
[Hugo 永久链接](https://blog.lxdlam.com/post/9cc3283b/)  

**图标**  
[Tabler Icons](https://tablericons.com/) (主题自带的图标来自这里)  
[Bootstrap Icons](https://icons.getbootstrap.com/)  
[Creative Commons icon download](https://creativecommons.org/about/downloads/) (CC协议的图标下载)
[Logo Worldvectorlogo](https://worldvectorlogo.com/zh/most-downloaded/1)  
[Wix Create a Stunning Logo Design](https://www.wix.com/createlogo6/logo_design)  

**hugo 主题的基础**  
[hugo - 变量和参数](https://gohugo.io/variables/)  
[hugo - 模板](https://gohugo.io/templates/)  
[golang 模板(template)的常用基本语法](https://studygolang.com/articles/8023)  