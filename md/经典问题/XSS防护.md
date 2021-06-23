<!--
 * @Author: tangdaoyong
 * @Date: 2021-06-22 22:39:47
 * @LastEditors: tangdaoyong
 * @LastEditTime: 2021-06-22 22:44:54
 * @Description: XSS 防护
-->
# XSS 防护

[XSS和CSRF防护](https://segmentfault.com/a/1190000038373771)

如何防范 XSS
总体就是不能将用户的输入直接存到服务器，需要对一些数据进行特殊处理

## 设置 HttpOnly
HttpOnly 是一个设置 cookie 是否可以被 javasript 脚本读取的属性，浏览器会禁止页面的 Javascript 访问带有 HttpOnly 属性的 Cookie。

严格来说，这种方式不是防御 XSS，而是在用户被 XSS 攻击之后，不被获取 Cookie 数据。

## CSP 内容安全策略

`CSP(content security policy)`，是一个额外的`安全层`，**用于检测并削弱某些特定类型的攻击，包括跨站脚本 (XSS) 和数据注入攻击等。**

CSP 可以通过 HTTP 头部（Content-Security-Policy）或<meta>元素配置页面的内容安全策略，以控制浏览器可以为该页面获取哪些资源。比如一个可以上传文件和显示图片页面，应该允许图片来自任何地方，但限制表单的 action 属性只可以赋值为指定的端点。

现在主流的浏览器内置了防范 XSS 的措施，开启 CSP，即开启白名单，可阻止白名单以外的资源加载和运行

## 输入检查
对于用户的任何输入要进行编码、解码和过滤：

编码：不能对用户输入的内容都保持原样，对用户输入的数据进行字符实体编码转义
解码：原样显示内容的时候必须解码，不然显示不到内容了
过滤：把输入的一些不合法的东西都过滤掉，从而保证安全性。如移除用户上传的 DOM 属性，如 onerror，移除用户上传的 Style 节点、iframe、script 节点等
对用户输入所包含的特殊字符或标签进行编码或过滤，如 <，>，script，防止 XSS 攻击

function escHTML(str) {
  if (!str) return ''
  return str
    .replace(/&/g, '&amp;')
    .replace(/</g, '&lt;')
    .replace(/>/g, '&gt;')
    .replace(/x27/g, '&#039;')
    .replace(/x22/g, '&quto;')
}
## 输出检查
用户的输入会存在问题，服务端的输出也会存在问题。一般来说，除富文本的输出外，在变量输出到 HTML 页面时，可以使用编码或转义的方式来防御 XSS 攻击。例如利用 sanitize-html 对输出内容进行有规则的过滤之后再输出到页面中。

输入内容长度控制
对于不受信任的输入，都应该限定一个合理的长度。虽然无法完全防止 XSS 发生，但可以增加 XSS 攻击的难度。

## 验证码

防止脚本冒充用户提交危险操作。
