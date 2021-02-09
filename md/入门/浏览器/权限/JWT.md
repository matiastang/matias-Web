<!--
 * @Author: tangdaoyong
 * @Date: 2021-02-09 11:07:28
 * @LastEditors: tangdaoyong
 * @LastEditTime: 2021-02-09 17:23:44
 * @Description: JWT
-->
# JWT

[JWT参考](https://zhuanlan.zhihu.com/p/86937325)
[JWT单点登录设计](https://mp.weixin.qq.com/s?__biz=MjM5MDc4MzgxNA==&mid=2458459482&idx=1&sn=13814af925cd20775c1cb0ba094a9e61&chksm=b1c23d7386b5b465995aae7c822e0d596685b4dbf16bc4143f105d0e03b38eb970b5fc9b7e26&scene=132#wechat_redirect)

## JWT与OAuth的区别

* `OAuth2`是一种授权框架，`OAuth2`用在使用第三方账号登录的情况(比如使用`weibo`, `qq`, `github`登录某个`app`)。
* `JWT`是一种认证协议，`JWT`是用在前后端分离, 需要简单的对后台`API`进行保护时使用。

**注意**无论使用哪种方式切记用`HTTPS`来保证数据的安全性

