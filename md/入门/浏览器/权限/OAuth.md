<!--
 * @Author: tangdaoyong
 * @Date: 2021-02-09 11:10:08
 * @LastEditors: tangdaoyong
 * @LastEditTime: 2021-02-09 11:37:35
 * @Description: OAuth
-->
# OAuth

[OAuth 百度百科](https://baike.baidu.com/item/oAuth/7153134?fr=aladdin)
[OAuth 2.0 的一个简单解释](http://www.ruanyifeng.com/blog/2019/04/oauth_design.html)
[OAuth 2.0 的四种方式](http://www.ruanyifeng.com/blog/2019/04/oauth-grant-types.html)
[Oauth2详解-介绍(一)](https://www.jianshu.com/p/84a4b4a1e833)
## 介绍

### OAuth

`OAuth`协议为用户资源的授权提供了一个安全的、开放而又简易的标准。与以往的授权方式不同之处是`OAuth`的授权不会使第三方触及到用户的帐号信息（如用户名与密码），即第三方无需使用用户的用户名与密码就可以申请获得该用户资源的授权，因此`OAuth`是安全的。`OAuth`是`Open Authorization`的简写。

`OAuth` 就是一种授权机制。数据的所有者告诉系统，同意授权第三方应用进入系统，获取这些数据。系统从而产生一个短期的进入令牌（`token`），用来代替密码，供第三方应用使用。

`OAuth` 的核心就是向第三方应用颁发令牌。

### OAuth2.0

`OAuth2.0`是`OAuth`协议的延续版本，但不向后兼容`OAuth 1.0`即完全废止了`OAuth1.0`。

`OAuth 2.0` 规定了四种获得令牌的流程。你可以选择最适合自己的那一种，向第三方应用颁发令牌。下面就是这四种授权方式。

1. 授权码（authorization-code）
2. 隐藏式（implicit）
3. 密码式（password）：
4. 客户端凭证（client credentials）

## OAuth与JWT的区别

* `OAuth2`是一种授权框架，`OAuth2`用在使用第三方账号登录的情况(比如使用`weibo`, `qq`, `github`登录某个`app`)。
* `JWT`是一种认证协议，`JWT`是用在前后端分离, 需要简单的对后台`API`进行保护时使用。

**注意**无论使用哪种方式切记用`HTTPS`来保证数据的安全性