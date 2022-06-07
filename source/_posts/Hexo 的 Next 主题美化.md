---
title: Hexo 的 Next 主题美化
date: 2022-06-06 22:00:00
updated: 2022-06-06 22:00:00
tags: ["hexo"]
categories:
  - other
---

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/panda-banner-1.png)

<!-- more -->

## 01.NexT 主题的下载与安装

- 下载 [NexT](https://github.com/theme-next/hexo-theme-next/releases) 主题并将其解压到博客的 themes 目录下，修改名称为 next；
- 修改博客根目录下的 _config.yml，将 theme 项设置为 next;

## 02.设置 NexT 主题样式

编辑 next 主题目录下的 _config.yml 文件，打开相应 schema 的注释，细节如下。

```yaml
# ---------------------------------------------------------------
# Scheme Settings
# ---------------------------------------------------------------

# Schemes
#scheme: Muse
#scheme: Mist
#scheme: Pisces
scheme: Gemini
```

## 03.设置 Hexo 使用中文

编辑博客根目录下的 _config.yml 文件，修改 language 项，细节如下。

```yaml
language: zh-CN
```

## 04.设置 NexT 主题头像

编辑 next 主题目录下的 _config.yml 文件，具体设置如下（`images` 位于 `themes/next/source` 下）。

```yaml
avatar:
  # 头像图片
  url: /images/gakki.png
  # 头像为圆形
  rounded: true
  # 鼠标移动到头像时，头像旋转
  rotated: true
```

## 05.设置博客标题、作者信息

编辑博客根目录下的 _config.yml 文件，具体设置如下。

```yaml
title: Leo's Blog #标题
subtitle: '坐标杭州，卖马为生' #子标题
author: Leo #作者信息
```

## 06.设置博客动态背景

启用 three 背景的方式如下：

```yaml
# 1.进入 next 主题目录
cd themes/next

# 2.拉取 three 背景依赖
git clone https://github.com/theme-next/theme-next-three source/lib/three

# 3.编辑 next 主题目录下的 _config.yml 文件
three:
  enable: true
  three_waves: true
  canvas_lines: false
  canvas_sphere: false
```

启用 canvas-nest 背景的方式如下：

```yaml
# 1.进入 next 主题目录
cd themes/next

# 2.拉取 canvas-next 背景依赖
git clone https://github.com/theme-next/theme-next-canvas-nest source/lib/canvas-nest

# 3.编辑 next 主题目录下的 _config.yml 文件
canvas_nest:
  enable: true
  onmobile: true # Display on mobile or not
  color: "0,0,255" # RGB values, use `,` to separate
  opacity: 0.5 # The opacity of line: 0~1
  zIndex: -1 # z-index property of the background
  count: 150 # The number of lines, default 99
```

启用 canvas-ribbon 背景的方式如下：

```yaml
# 1.进入 next 主题目录
cd themes/next

# 2.拉取 canvas-ribbon 背景依赖
git clone https://github.com/theme-next/theme-next-canvas-ribbon source/lib/canvas-ribbon

# 3.编辑 next 主题目录下的 _config.yml 文件
canvas_ribbon:
  enable: true
  size: 300 # The width of the ribbon
  alpha: 0.6 # The transparency of the ribbon
  zIndex: -1 # The display level of the ribbon
```

> 提示1：要删除 clone 下来的依赖包中的 .git 文件夹
>
> 提示2：如果下载的依赖无法提交到 github，则可能是由 next 目录下的 .gitignore 引起的
>
> 提示3：我使用的动态背景是 canvas-nest

## 07.设置页面加载效果

```yaml
# 1.进入 next 主题目录
cd themes/next

# 2.拉取 three 背景依赖
git clone https://github.com/theme-next/theme-next-pace source/lib/pace

# 3.编辑 next 主题目录下的 _config.yml 文件
pace:
  enable: true
  # Themes list:
  # big-counter | bounce | barber-shop | center-atom 
  # center-circle | center-radar | center-simple
  # corner-indicator | fill-left | flat-top | flash 
  # loading-bar | mac-osx | material | minimal
  theme: center-atom
```

> 提示1：要删除 clone 下来的依赖包中的 .git 文件夹
>
> 提示2：如果下载的依赖无法提交到 github，则可能是由 next 目录下的 .gitignore 引起的

## 08.设置鼠标点击特效

首先在 themes/next/source/js/src 里面建一个叫 fireworks.js 的文件（`src` 文件夹需要自己创建），代码如下：

```javascript
"use strict";function updateCoords(e){pointerX=(e.clientX||e.touches[0].clientX)-canvasEl.getBoundingClientRect().left,pointerY=e.clientY||e.touches[0].clientY-canvasEl.getBoundingClientRect().top}function setParticuleDirection(e){var t=anime.random(0,360)*Math.PI/180,a=anime.random(50,180),n=[-1,1][anime.random(0,1)]*a;return{x:e.x+n*Math.cos(t),y:e.y+n*Math.sin(t)}}function createParticule(e,t){var a={};return a.x=e,a.y=t,a.color=colors[anime.random(0,colors.length-1)],a.radius=anime.random(16,32),a.endPos=setParticuleDirection(a),a.draw=function(){ctx.beginPath(),ctx.arc(a.x,a.y,a.radius,0,2*Math.PI,!0),ctx.fillStyle=a.color,ctx.fill()},a}function createCircle(e,t){var a={};return a.x=e,a.y=t,a.color="#F00",a.radius=0.1,a.alpha=0.5,a.lineWidth=6,a.draw=function(){ctx.globalAlpha=a.alpha,ctx.beginPath(),ctx.arc(a.x,a.y,a.radius,0,2*Math.PI,!0),ctx.lineWidth=a.lineWidth,ctx.strokeStyle=a.color,ctx.stroke(),ctx.globalAlpha=1},a}function renderParticule(e){for(var t=0;t<e.animatables.length;t++){e.animatables[t].target.draw()}}function animateParticules(e,t){for(var a=createCircle(e,t),n=[],i=0;i<numberOfParticules;i++){n.push(createParticule(e,t))}anime.timeline().add({targets:n,x:function(e){return e.endPos.x},y:function(e){return e.endPos.y},radius:0.1,duration:anime.random(1200,1800),easing:"easeOutExpo",update:renderParticule}).add({targets:a,radius:anime.random(80,160),lineWidth:0,alpha:{value:0,easing:"linear",duration:anime.random(600,800)},duration:anime.random(1200,1800),easing:"easeOutExpo",update:renderParticule,offset:0})}function debounce(e,t){var a;return function(){var n=this,i=arguments;clearTimeout(a),a=setTimeout(function(){e.apply(n,i)},t)}}var canvasEl=document.querySelector(".fireworks");if(canvasEl){var ctx=canvasEl.getContext("2d"),numberOfParticules=30,pointerX=0,pointerY=0,tap="mousedown",colors=["#FF1461","#18FF92","#5A87FF","#FBF38C"],setCanvasSize=debounce(function(){canvasEl.width=2*window.innerWidth,canvasEl.height=2*window.innerHeight,canvasEl.style.width=window.innerWidth+"px",canvasEl.style.height=window.innerHeight+"px",canvasEl.getContext("2d").scale(2,2)},500),render=anime({duration:1/0,update:function(){ctx.clearRect(0,0,canvasEl.width,canvasEl.height)}});document.addEventListener(tap,function(e){"sidebar"!==e.target.id&&"toggle-sidebar"!==e.target.id&&"A"!==e.target.nodeName&&"IMG"!==e.target.nodeName&&(render.play(),updateCoords(e),animateParticules(pointerX,pointerY))},!1),setCanvasSize(),window.addEventListener("resize",setCanvasSize,!1)}"use strict";function updateCoords(e){pointerX=(e.clientX||e.touches[0].clientX)-canvasEl.getBoundingClientRect().left,pointerY=e.clientY||e.touches[0].clientY-canvasEl.getBoundingClientRect().top}function setParticuleDirection(e){var t=anime.random(0,360)*Math.PI/180,a=anime.random(50,180),n=[-1,1][anime.random(0,1)]*a;return{x:e.x+n*Math.cos(t),y:e.y+n*Math.sin(t)}}function createParticule(e,t){var a={};return a.x=e,a.y=t,a.color=colors[anime.random(0,colors.length-1)],a.radius=anime.random(16,32),a.endPos=setParticuleDirection(a),a.draw=function(){ctx.beginPath(),ctx.arc(a.x,a.y,a.radius,0,2*Math.PI,!0),ctx.fillStyle=a.color,ctx.fill()},a}function createCircle(e,t){var a={};return a.x=e,a.y=t,a.color="#F00",a.radius=0.1,a.alpha=0.5,a.lineWidth=6,a.draw=function(){ctx.globalAlpha=a.alpha,ctx.beginPath(),ctx.arc(a.x,a.y,a.radius,0,2*Math.PI,!0),ctx.lineWidth=a.lineWidth,ctx.strokeStyle=a.color,ctx.stroke(),ctx.globalAlpha=1},a}function renderParticule(e){for(var t=0;t<e.animatables.length;t++){e.animatables[t].target.draw()}}function animateParticules(e,t){for(var a=createCircle(e,t),n=[],i=0;i<numberOfParticules;i++){n.push(createParticule(e,t))}anime.timeline().add({targets:n,x:function(e){return e.endPos.x},y:function(e){return e.endPos.y},radius:0.1,duration:anime.random(1200,1800),easing:"easeOutExpo",update:renderParticule}).add({targets:a,radius:anime.random(80,160),lineWidth:0,alpha:{value:0,easing:"linear",duration:anime.random(600,800)},duration:anime.random(1200,1800),easing:"easeOutExpo",update:renderParticule,offset:0})}function debounce(e,t){var a;return function(){var n=this,i=arguments;clearTimeout(a),a=setTimeout(function(){e.apply(n,i)},t)}}var canvasEl=document.querySelector(".fireworks");if(canvasEl){var ctx=canvasEl.getContext("2d"),numberOfParticules=30,pointerX=0,pointerY=0,tap="mousedown",colors=["#FF1461","#18FF92","#5A87FF","#FBF38C"],setCanvasSize=debounce(function(){canvasEl.width=2*window.innerWidth,canvasEl.height=2*window.innerHeight,canvasEl.style.width=window.innerWidth+"px",canvasEl.style.height=window.innerHeight+"px",canvasEl.getContext("2d").scale(2,2)},500),render=anime({duration:1/0,update:function(){ctx.clearRect(0,0,canvasEl.width,canvasEl.height)}});document.addEventListener(tap,function(e){"sidebar"!==e.target.id&&"toggle-sidebar"!==e.target.id&&"A"!==e.target.nodeName&&"IMG"!==e.target.nodeName&&(render.play(),updateCoords(e),animateParticules(pointerX,pointerY))},!1),setCanvasSize(),window.addEventListener("resize",setCanvasSize,!1)};
```

打开 themes/next/layout/_layout.swig ,在 `</body>` 标签上方添加如下代码：

```html
{% if theme.fireworks %}
   <canvas class="fireworks" style="position: fixed;left: 0;top: 0;z-index: 1; pointer-events: none;" ></canvas> 
   <script type="text/javascript" src="//cdn.bootcss.com/animejs/2.2.0/anime.min.js"></script> 
   <!-- js 脚本的地址需要根据具体情况进行调整 -->
   <script type="text/javascript" src="/js/src/fireworks.js"></script>
{% endif %}
```

打开 themes/next/_config.yml，在文件末尾添加如下代码：

```yaml
# Fireworks
fireworks: true
```

> 提示1：如果 src 目录不存在，则需要手动创建
>
> 提示2：在 _layout.swig 中添加代码时，注意是否需要调整 js 脚本的路径。如果博客的 root 地址是 / 则无需修改，如过博客的 root 地址是 /blog/，则 js 脚本的地址前面就需要加上 /blog 

## 09.取消目录中标题自动编号

打开 themes/next/_config.yml，具体配置如下：

```yaml
toc:
  enable: true
  number: false
  wrap: false
  expand_all: false
  max_depth: 6
```

## 10.隐藏页面底部文字

打开 themes/next/_config.yml，具体配置如下：

```yaml
# Powered by Hexo & NexT
powered: false
```

打开 `themes/next/layout/_partials/footer.swig`，注释掉如下内容：

```html
<!--
<div class="copyright">
  ......
</div>
-->
```

## 11.设置博客页面透明度

打开 themes\next\source\css\\\_schemes\Pisces\\_layout.styl

```css
.header-inner {
  background: rgba(255,255,255,0.3);
  ......
}
```

打开 themes\next\source\css\\_schemes\Gemini\index.styl

```css
.content-wrap {
  background: rgba(255,255,255,0.3);
  ......
}

.post-block {
  background: rgba(255,255,255,0.3);
  ......
}

.comments {
  background: rgba(255,255,255,0.3);
  ......
}

.pagination {
  ......
  background: rgba(255,255,255,0.3);
  ......
}
```

打开 themes\next\source\css\\\_schemes\Pisces\\_sidebar.styl

```css
.sidebar {
  background: rgba(255,255,255,0.3);
  ......
}

.sidebar-inner {
  background: rgba(255,255,255,0.3);
  ......
}
```

## 12.显示文章阅读进度

打开 themes/next/_config.yml，具体配置如下：

```yaml
# 会在 back2top 按钮上显示阅读百分比
back2top:
  enable: true
  # Back to top in sidebar.
  sidebar: false
  # Scroll percent label in b2t button.
  scrollpercent: true

# 会在页面上方显示一个阅读进度条
reading_progress:
  enable: true
  # Available values: top | bottom
  position: top
  color: "#37c6c0"
  height: 3px
```

> 提示：没必要都开启，我选择在 back2top 按钮上显示阅读百分比

## 13.启用本地搜索

在 `.github/workflows/pages.yml` 中添加如下配置（需要注意添加的位置）：

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/install-hexo-generator-searchdb.png)

编辑博客根目录下的 _config.yml 文件，在底部添加如下配置：

```yaml
# Local Searh
search:
  path: search.xml
  field: post
  content: true
  format: html
```

打开 themes/next/_config.yml，启用本地搜索功能：

```yaml
local_search:
  enable: true
  # If auto, trigger search by changing input.
  # If manual, trigger search by pressing enter key or search button.
  trigger: auto
  # Show top n results per article, show all results by setting to -1
  top_n_per_article: 1
  # Unescape html strings to the readable one.
  unescape: false
  # Preload the search data when the page loads.
  preload: false
```

## 14.启用标签页

打开 themes/next/_config.yml，启用标签页：

```yaml
menu:
  home: / || home
  #about: /about/ || user
  tags: /tags/ || tags
  #categories: /categories/ || th
  archives: /archives/ || archive
  #schedule: /schedule/ || calendar
  #sitemap: /sitemap.xml || sitemap
  #commonweal: /404/ || heartbeat
```

在博客根目录的 source 文件夹下创建一个名为 tags 的文件夹，在 tags 文件夹下创建一个 index.md 文件，文件内容如代码块所示，需要注意的是 type 的值必须写 tags，title 是标签页的标题，可以随意起。

```
---
title: 标签 
date: 2020-01-28 17:40:00
type: "tags"
---
```

给文章添加标签非常简单，不需要用户去将标签预先创建出来，只要在文章开头的描述内容中指定，相关标签就会自动生成。如下方代码块所示，这里为文章打了两个标签。

```
---
title: Hexo 的 NexT 主题美化
date: 2020-01-28 10:20:20
updated: 2020-01-28 16:16:16
tags: [hexo,github]
---
```

文章的标签还可以通过另一种方式来指定，如下方代码所示。

```
---
title: Hexo 的 NexT 主题美化
date: 2020-01-28 10:20:20
updated: 2020-01-28 16:16:16
tags: 
  - hexo
  - github
---
```

## 15.启用分类页

打开 themes/next/_config.yml，启用标签页：

```yaml
menu:
  home: / || home
  #about: /about/ || user
  tags: /tags/ || tags
  categories: /categories/ || th
  archives: /archives/ || archive
  #schedule: /schedule/ || calendar
  #sitemap: /sitemap.xml || sitemap
  #commonweal: /404/ || heartbeat
```

在博客根目录的 source 文件夹下创建一个名为 categories 的文件夹，在 categories 文件夹下创建一个 index.md 文件，文件内容如代码块所示，需要注意的是 type 的值必须写 categories，title 是分类页的标题，可以随意起。

```yaml
---
title: 分类 
date: 2020-01-28 17:40:00
type: "categories"
---
```

与为文章添加标签类似，为文章添加所属类别也只需在文章开头的描述内容中指定，示例如下。

```
---
title: Hexo 的 NexT 主题美化
date: 2020-01-28 10:20:20
updated: 2020-01-28 16:16:16
categories: 
  - 大数据
---
```

与一篇文章可以有多个标签不同，一篇文章只能属于一个类别，如果在指定类别时，指定了多个分类，那么就会形成层级关系，如下方代码块所示，该写法表示文章所属类别为 a/b。

```
---
title: Hexo 的 NexT 主题美化
date: 2020-01-28 10:20:20
updated: 2020-01-28 16:16:16
categories: 
  - a
  - b
---
```

## 16.设置网站图标

准备好两张图标，尺寸为 16x16 与 32x32。将下载好的图标放到 themes\next\source\images 目录中，然后打开 themes/next/_config.yml，设置网站图标。

```yaml
favicon:
  small: /images/paw-16x16-next.png
  medium: /images/paw-32x32-next.png
```

> 推荐：图标可以到 iconfont 下载

## 17.设置代码高亮主题

打开 themes/next/_config.yml，具体配置如下：

```yaml
codeblock:
  highlight_theme: galactic
  copy_button:
    enable: true
    show_result: true
    style:
```

## 18.公益404页面

新建 404.html，放到主题的 `source` 目录下，内容如下：

```html
<!DOCTYPE HTML>
<html>
<head>
  <meta http-equiv="content-type" content="text/html;charset=utf-8;"/>
  <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />
  <meta name="robots" content="all" />
  <meta name="robots" content="index,follow"/>
  <link rel="stylesheet" type="text/css" href="https://qzone.qq.com/gy/404/style/404style.css">
</head>
<body>
  <script type="text/plain" src="http://www.qq.com/404/search_children.js"
          charset="utf-8" homePageUrl="/blog/"
          homePageName="回到我的主页">
  </script>
  <script src="https://qzone.qq.com/gy/404/data.js" charset="utf-8"></script>
  <script src="https://qzone.qq.com/gy/404/page.js" charset="utf-8"></script>
</body>
</html>
```

## 19.隐藏文章底部#标签

打开 themes\next\layout\_macro\post.swig，注释掉如下内容：

```css
<!--
  {%- if post.tags and post.tags.length %}
    {%- if theme.tag_icon %}
      {%- set tag_indicate = '<i class="fa fa-tag"></i>' %}
    {% else %}
      {%- set tag_indicate = '#' %}
    {%- endif %}
    <div class="post-tags">
      {%- for tag in post.tags.toArray() %}
        <a href="{{ url_for(tag.path) }}" rel="tag">{{ tag_indicate }} {{ tag.name }}</a>
      {%- endfor %}
    </div>
  {%- endif %}
-->
```

## 20.去除顶部黑线

打开 `themes/next/layout/_layout.swig`，注释掉如下代码：

```html
<!-- 
<div class="headband"></div> 
-->
```

## 21.启用关于页

打开 `themes/next/_config.yml`，启用关于页：

```yaml
menu:
  home: / || home
  about: /about/ || user
  tags: /tags/ || tags
  categories: /categories/ || th
  archives: /archives/ || archive
  #schedule: /schedule/ || calendar
  #sitemap: /sitemap.xml || sitemap
  #commonweal: /404/ || heartbeat
```

在博客根目录的 source 文件夹下创建一个名为 about 的文件夹，在 about 文件夹下创建一个 index.md 文件，文件头如代码块所示：

```yaml
---
title: about
date: 2022-06-07 14:48:56
---
```

## 22.设置看板娘

首先，拉取 `live2d-widget` 项目到 `themes/next/source` 目录下（记得删除 `.git` 文件夹）：

```shell
git clone https://github.com/stevenjoezhang/live2d-widget.git source/live2d-widget
```

然后，打开`live2d-widget` 项目中的 `autoload.js` 文件，修改指定代码：

```js
// 修改前
const live2d_path = "https://fastly.jsdelivr.net/gh/stevenjoezhang/live2d-widget@latest/";

// 修改后
const live2d_path = "/live2d-widget/";
```

接着，打开 `themes/next/layout/_layout.swig` 文件， 在 `</body>` 标签上方添加如下代码：

```html
<script src="/live2d-widget/autoload.js"></script>
```

最后，打开主题配置文件 `themes/next/_config.yml` ，在其中末尾添加：

```yaml
live2d:
  enable: true
```

## 23.设置标签云

在 `.github/workflows/pages.yml` 中添加如下配置（需要注意添加的位置）：

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/install-hexo-tag-cloud.png)

打开 `next/layout/_macro/sidebar.swig`，添加如下代码：

```html
{% if site.tags.length > 1 %}
  <script type="text/javascript" charset="utf-8" src="{{ url_for('/js/tagcloud.js') }}"></script>
  <script type="text/javascript" charset="utf-8" src="{{ url_for('/js/tagcanvas.js') }}"></script>
  <div class="widget-wrap">
    <h3 class="widget-title">Tag Cloud</h3>
    <div id="myCanvasContainer" class="widget tagcloud">
      <canvas width="250" height="250" id="resCanvas" style="width:100%">
        {{ list_tags() }}
      </canvas>
    </div>
  </div>
{% endif %}
```

添加位置如下图所示：

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/hexo-tag-cloud-in-sidebar-swig.png)

打开博客根目录下的 `_config.yml` 文件，添加如下设置：

```yaml
# hexo-tag-cloud
tag_cloud:
    textFont: 'Trebuchet MS, Helvetica'
    textColor: '#333'
    textHeight: 25
    outlineColor: '#E2E1D1'
    maxSpeed: 0.1 # range from [0.01 ~ 1]
    pauseOnSelected: false # true means pause the cloud tag movement when highlight a tag
```

## 24.参考资源

- [主题官网-新](https://theme-next.org/)
- [主题官网-旧](http://theme-next.iissnan.com/)
- [使用Hexo博客搭建的个人博客，使用Next主题来进行优化改造](https://www.bilibili.com/video/av17653359)
- [Hexo-NexT配置超炫网页效果](https://www.jianshu.com/p/9f0e90cc32c2)
- [hexo的next创建关于我](https://blog.csdn.net/lcyaiym/article/details/76768762?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2~default~CTRLIST~default-1-76768762-blog-61414438.pc_relevant_default&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2~default~CTRLIST~default-1-76768762-blog-61414438.pc_relevant_default&utm_relevant_index=1)
- [Next主题中添加超级好玩的看板娘（CDN方式）](https://blog.51cto.com/u_13640625/3032364)
- [Hexo博客+Next主题深度优化与定制](https://www.ngui.cc/51cto/show-540008.html)
- [stevenjoezhang/live2d-widget: 把萌萌哒的看板娘抱回家 (ノ≧∇≦)ノ](https://github.com/stevenjoezhang/live2d-widget)
- [Hexo博客建立标签云及效果展示](https://cloud.tencent.com/developer/article/1934183)
- [D0n9X1n/hexo-tag-cloud: Yet, just another tag cloud plugin for hexo](https://github.com/D0n9X1n/hexo-tag-cloud)