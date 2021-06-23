<!--
 * @Author: tangdaoyong
 * @Date: 2021-06-22 22:42:32
 * @LastEditors: tangdaoyong
 * @LastEditTime: 2021-06-22 22:44:25
 * @Description: CSRF防护
-->
# CSRF防护

[XSS和CSRF防护](https://segmentfault.com/a/1190000038373771)

## 验证码
由于 CSRF 攻击伪造请求不会经过受攻击的网站，所以我们可以在网站加入验证码，这样必须通过验证码之后才能进行请求，有效的遏制了 CSRF 请求。

但是，这种方式不是万能的，并不是每个请求都加验证码，那样用户体验会非常不好，只能在部分请求添加，作为一种辅助的防御手段。

## 验证 Referer
在 HTTP 协议中，头部有个 Referer 字段，他记录了该 HTTP 请求的来源地址，在服务端设置该字段的检验，通过检查该字段，就可以知道该请求是否合法，不过请求头也容易伪造。

## cookie 设置 SameSite
设置 SameSite：设置 cookie 的 `SameSite` 值为 strict，这样只有`同源网站`的请求才会带上 cookie。这样 cookies 就不能被其他域名网站使用，达到了防御的目的。

## 添加 token 验证
浏览器请求服务器时，服务器返回一个 token，每个请求都需要同时带上 token 和 cookie 才会被认为是合法请求

这是一种相对成熟的解决方案。要抵御 CSRF，关键在于在请求中放入攻击者所不能伪造的信息，并且该信息不存在于 Cookie 之中。在服务端随机生成 token，在 HTTP 请求中以参数的形式加入这个 token，并在服务器端建立一个拦截器来验证这个 token，如果请求中没有 token 或者 token 内容不正确，则认为可能是 CSRF 攻击而拒绝该请求。

## 更换登录态方案
因为 CSRF 本质是伪造请求携带了保存在 cookies 中的信息，所以对 session 机制的登录态比较不利，如果更换 `JWT（JSON Web Token）`方案，其 token 信息一般设置到 HTTP 头部的，所以可以防御 CSRF 攻击。