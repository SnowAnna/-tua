项目中经常会出现的一种情况，有一个列表，譬如是案例列表，点击列表中的某一项，跳转至详情页面。详情是根据所点击的某条记录生成的，因为案例和具体的详情页面，都是用户后期自行添加的，我们开始编写时，不可能穷尽。因此跳转页面时，我们需要传递一个参数过去，这样我们才能通过这个参数进行数据请求，然后根据后台返回的数据来生成页面。因此，通过a标签跳转的方式，肯定是行不通的。 
我们经常写form表单，提交时，可以传递参数，如果使用表单，并将其隐藏起来，应该可以达到效果。 
除此以外，window.location.href和window.open也可以达到效果。

1、通过form表单传递参数

<html lang="en">
    <head>
    <!--网站编码格式，UTF-8 国际编码，GBK或 gb2312 中文编码-->
        <meta http-equiv="content-type" content="text/html;charset=utf-8" />
        <meta name="Keywords" content="关键词一，关键词二">
        <meta name="Description" content="网站描述内容">
        <meta name="Author" content="Yvette Lau">
        <title>Document</title>
        <!--css js 文件的引入-->
        <!-- <link rel="shortcut icon" href="images/favicon.ico">        -->
        <link rel="stylesheet" href=""/>
        <script type = "text/javascript" src = "jquery-1.11.2.min.js"></script> 
    </head>
    <body>      
        <form name = "frm" method = "get" action = "receive.html" onsubmit = "return foo()" style = "position:relative;">
            <input type="hidden"  name="hid" value = "" index = "lemon"> 
            <img class = "more" src = "main_jpg10.png" />
            <input type = "submit" style = "position:absolute;left:10px;top:0px;width:120px;height:40px;opacity:0;cursor:pointer;"/>
        </form>     
        <form name = "frm" method = "get" action = "receive.html" onsubmit = "return foo()" style = "position:relative;">
            <input type="hidden"  name="hid" value = "" index = "aaa"> 
            <img class = "more" src = "main_jpg10.png" />
            <input type = "submit" style = "position:absolute;left:10px;top:0px;width:120px;height:40px;opacity:0;cursor:pointer;"/>
        </form>
        <form name = "frm" method = "get" action = "receive.html" onsubmit = "return foo()" style = "position:relative;">
            <input type="hidden"  name="hid" value = "" index = "bbb"> 
            <img class = "more" src = "main_jpg10.png" />
            <input type = "submit" style = "position:absolute;left:10px;top:0px;width:120px;height:40px;opacity:0;cursor:pointer;"/>
        </form>
    </body>
</html>
<script>
    function foo(){
        var frm = window.event.srcElement;
        frm.hid.value = $(frm.hid).attr("index"); 
        return true;
    }
</script>
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
点击图片时，跳转至receive.html页面。页面的url变成： 
这里写图片描述 
我们想要传的字符串已经传递了过来。 
然后再对当前的url进行字符串分割 
window.location.href.split(“=”)[1]//得到lemon 
我们拿到需要传来的参数之后，就可以根据这个进行下一步的处理了。 
除了上述通过字符串分割来获取url传递的参数外，我们还可以通过正则匹配和window.location.search方法来获取。

2、通过window.location.href

譬如我们点击某个列表，需要传递一个字符串到detail.html页面，然后detail.html页面根据传来的值，通过ajax交互数据，加载页面的内容。

var index = "lemon"; var url = "receive.html?index="+index; $("#more").click(function(){ window.location.href = url; });
当前页面会被替换成recieve.html的页面，页面的url变为： 
这里写图片描述 
然后我们再用上面的方法提取自己需要的参数

3、通过window.location.open

如果是希望打开一个新页面，而不是改变当前的页面，那么window.location.href就不适用了，此时，我们需要借助于window.location.open()来实现 
简单介绍有一下window.open()函数，window.open()有三个参数，第一个参数是要打开的页面的url,第二个参数是窗口目标，第三个参数是一个特定字符串以及一个表示新页面是否取代浏览器历史集中当前加载页面的布尔值，通过只需要传递第一个参数。第二个参数还可以是”_blank”,”_self”,”_parent”,”_top”这样的特殊窗口名称，”_blank”打开新窗口,”_self”实现的效果同window.location.href. 
继续上面的例子：

<script>
    var index = "lemon";
    var url = "receive.html?index="+index;
    $("#more").click(function(){
        window.open(url)
    });
</script>
1
2
3
4
5
6
7
这样在点击的时候，就会打开一个新页面，页面的url地址与上面相同。 
由于浏览器的安全限制，有些浏览器在弹出窗口配置方面增加限制，大多数浏览器都内置有弹出窗口的屏蔽程序，因此，弹出窗口有可能被屏蔽，在弹出窗口被屏蔽时，需要考虑两种可能性，一种是浏览器内置的屏蔽程序阻止弹出窗口，那么 window.open()很可能返回Null，此时，只要监测这个返回的值就可以确定弹出窗口是否被屏蔽。

var newWin = window.open(url);
if(newWin == null){
    alert("弹窗被阻止");
}
1
2
3
4
另一种是浏览器扩展或其他程序阻止的弹出窗口，那么window.open()通常会抛出一个错误，因此，要像准确的检测弹出窗口是否被屏蔽，必须在检测返回值的同时，将window.open()封装在try-catch块中，上面的例子中可以写成如下形式：

<script>
    var blocked = false;
    try{
        var index = "lemon";
        var url = "receive.html?index="+index;
        $("#more").click(function(){
           var newWin = window.open(url);
           if(newWin == null){
               blocked = true;
           }
        });
    } catch(ex){
        block = true;
    }
    if(blocked){
        alert("弹出窗口被阻止");
    }    
</script>