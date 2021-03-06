<nav id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#orgheadline1">1. 前言</a></li>
<li><a href="#orgheadline2">2. 注释</a></li>
<li><a href="#orgheadline3">3. 变量</a></li>
<li><a href="#orgheadline4">4. for语句</a></li>
<li><a href="#orgheadline6">5. 条件分支</a>
<ul>
<li><a href="#orgheadline5">5.1. tests</a></li>
</ul>
</li>
<li><a href="#orgheadline7">6. html特殊符号问题</a></li>
<li><a href="#orgheadline8">7. 模板文件继承机制</a></li>
<li><a href="#orgheadline9">8. jinja2提供的一些全局函数</a></li>
<li><a href="#orgheadline10">9. filters</a></li>
<li><a href="#orgheadline11">10. 国际化</a></li>
<li><a href="#orgheadline13">11. jinja2模板系统</a>
<ul>
<li><a href="#orgheadline12">11.1. 加入错误页面</a></li>
</ul>
</li>
</ul>
</div>
</nav>


# 前言<a id="orgheadline1"></a>

因为我们使用jinja2模板系统主要是在flask框架里面，所以jinja2提供的api接口我们不需要太多了解了，只需要知道在flask框架中如何使用模板文件即可。本文主要讨论如何编写模板文件的细节。另外jinja2模板系统也不一定局限于html文件，只要是任何txt文本即可，所以如果读者有兴趣，利用jinja2模块系统弄出一个latex或者emacs的org生成器也是可以的。

# 注释<a id="orgheadline2"></a>

这里面的内容是模板文件的注释内容。

    {# ... #}

# 变量<a id="orgheadline3"></a>

这里算是jinja2模板的主体内容了，里面的变量可以直接使用，在render的时候传进去即可。然后 `object.a` 这样的dot法引用，或者 `object['a']` 这样的利用对象 `__getitem__` 内置方法来调用值的形式也支持。

    {{ ... }}

变量的值在jinja2模板系统中调用之后将会变成字符串然后插入在文档中，这就不多说了。

# for语句<a id="orgheadline4"></a>

for语句结构如下所示:

    <ul>
    {% for user in users %}
      <li>{{ user.username }}</li>
    {% endfor %}
    </ul>

此外jinja2还有一种写法，这两种写法是等价的:

    <ul>
    # for user in users
      <li>{{ user.username }}</li>
    # endfor
    </ul>

# 条件分支<a id="orgheadline6"></a>

条件分支主要用于有条件的显示某些内容。

## tests<a id="orgheadline5"></a>

测试器

# html特殊符号问题<a id="orgheadline7"></a>

如果你的变量有这些html的特殊符号:

    <  >  &  "

假设你没有在flask中使用jinja2模板系统，那么这些字符是没有经过特殊处理的，那么比如说 `<b>test</b>` 这段字符串到了html文档中就将以粗体的形式显示。如果你希望显示这些符号，那么可以使用 **escape** 过滤器来做到这点:

    {{ test|escape }}

或者:

    {{ test|e }}

都是一样的。

这里的过滤器有点类似bash的管道的意思，意思是将输出的字符串经过额外的操作。

但一般推荐的风格是html标签都放在jinja2模板系统的外面，jinja2模板系统只处理最核心的那些字符串。所以flask是设置为全局auto escape的，这是正确的思路。如果你确实有某些html标签就希望是html标签的形式显示出来，而不经过escape，那么可以采用 **safe** 过滤器<sup><a id="fnr.1" class="footref" href="#fn.1">1</a></sup> 。

    {{ "<b>test</b>"|safe }}

# 模板文件继承机制<a id="orgheadline8"></a>

jinja2的模板文件有一种继承机制，可以让你基于某个模板文件来建构出另外一个模板文件，前面那个模板文件大概可以称作模板文件的模板文件吧。具体使用是在父模板（模板文件的模板文件）构建一些block区块，如下所示:

    <title>{% block title %} {% endblock %}</title>

这里构建了一个title区块。

然后子模板首先继承父模板所有的内容:

    {% extends "base.html" %}

然后一些需要定制的部分，比如说这里的title部分，做成block之后，子模板文件可以重新定义这个title block:

    {% extends "base.html" %}
    {% block title}books - the classic books of  which you want to collected
    {% endblock %}

此外子模块在block重载的时候，你还可以用

    {{ super() }}

来加载父模块在该block中的一些定义。

上面title block的内容你可以如下引用之:

    {{ self.title() }}

# jinja2提供的一些全局函数<a id="orgheadline9"></a>

# filters<a id="orgheadline10"></a>

过滤器就是一些额外的字符串操作函数，一般推荐还是在python代码中把要输出显示的字符串处理好吧，下面列出一些函数简单了解下即可。

# 国际化<a id="orgheadline11"></a>

# jinja2模板系统<a id="orgheadline13"></a>

{{ x }}   x是个变量，等下将被替换为字符串。

rend\_template(what,key=value)

Hello, {{ name|capitalize }} 

safe  后面再详解   
capitalize  首字母大写，其他字母小写。
lower
upper
title 每个word都capitalize
trim 前后空格去除
striptags html tags 去除

safe  比如说 <h1> &#x2026; 将不会被解析

分支

    {% if user %}
    Hello, {{ user }}!
    {% else %}
    Hello, Stranger!
    {% endif %}

for语句

    <ul>
    {% for comment in comments %}
    <li>{{ comment }}</li>
    {% endfor %}
    </ul>

宏 和python的函数类似，遇到即将其展开。

    {% macro render_comment(comment) %}
    <li>{{ comment }}</li>
    {% endmacro %}
    <ul>
    {% for comment in comments %}
    {{ render_comment(comment) }}
    {% endfor %}
    </ul>

外部宏文件 引入

    {% import 'macros.html' as macros %}

    {% import 'macros.html' as macros %}
    <ul>
    {% for comment in comments %}
    {{ macros.render_comment(comment) }}
    {% endfor %}
    </ul>

引入

    {% include 'common.html' %}

模板的继承
base.html

    <html>
    <head>
    {% block head %}
    <title>{% block title %}{% endblock %} - My Application</title>
    {% endblock %}
    </head>
    <body>
    {% block body %}
    {% endblock %}
    </body>
    </html>

    {% extends "base.html" %}
    {% block title %}Index{% endblock %}
    {% block head %}
    {{ super() }}
    <style>
    </style>
    {% endblock %}
    {% block body %}
    <h1>Hello, World!</h1>
    {% endblock %}

后面重定义的block就是原地插入的意思，super()来保留原来的内容。

## 加入错误页面<a id="orgheadline12"></a>

    @app.errorhandler(404)
    def page_not_found(e):
        return render_template('404.html'),404

<div id="footnotes">
<h2 class="footnotes">Footnotes: </h2>
<div id="text-footnotes">

<div class="footdef"><sup><a id="fn.1" class="footnum" href="#fnr.1">1</a></sup> <div class="footpara">参考了 [这个网页](http://stackoverflow.com/questions/3206344/passing-html-to-template-using-flask-jinja2) 。</div></div>


</div>
</div>