---
title: 登录相关技术调研
description: OAuth，CAS，OIDC
categories:
  - 架构
date: 2021-06-13 11:20:19
tags:
  - CS
---

## 开放授权 OAuth（ The OAuth 2.0 Authorization Framework）
[rfc6749](https://datatracker.ietf.org/doc/html/rfc6749)
授权码模式（authorization code）：
1. 在你的应用中，让用户访问登录链接，浏览器跳转到 授权 服务器完成认证。
2. 浏览器接收到一个从 授权 服务器发来的授权码。
3. 浏览器通过重定向将授权码发送到你的应用后端。
4. 你的应用服务将授权码发送到 授权服务器 获取 AccessToken，如果需要，还会返回 refresh token。
5. 你的应用后端现在知道了用户的身份，后续可以保存用户信息，重定向到前端其他页面，使用 AccessToken 调用资源方的其他 API 等等。

隐藏式（implicit）
1. 跳过了”授权码”这个步骤，所有步骤在浏览器中完成。

使用场景：允许用户让第三方应用访问该用户在某一网站上存储的私密的资源，而无需将用户名和密码提供给第三方应用，主要用于资源授权

## CAS Protocol
[CAS Protocol](https://apereo.github.io/cas/6.3.x/protocol/CAS-Protocol.html)
1. 局部会话（Application Session）
2. 全局会话（CAS Session）
3. Ticket Granted Ticket，TGT：大令牌，记录某用户全局会话的状态
4. Service Ticket，ST：小令牌，用来向CAS认证服务器（用户中心）兑换用户信息。
5. CAS-Server（TGT）超时时间 >= CAS-Client的超时时间

使用场景：解决一次登录，跨域名多处登录场景，客户端单独建立会话，多站点会话同步需要额外逻辑。

## OIDC
OpenID Connect 协议，基于 OAuth 2.0 的身份认证协议，增加了 Id Token，能够认证用户并完成资源授权。授权流程与 OAuth 2.0 一样，主要区别在于 OIDC 授权流程中会额外返回 Id Token。

- OpenID Provider：授权服务器，负责颁发Id Token。
- 终端用户：Id Token 的信息中会包含终端用户的信息。
- 调用方：请求 Id Token 的应用。
- Id Token：由 OpenID Provider 颁发，包含关于终端用户的信息字段。
- Claim：指终端用户信息字段。

## IdentityServer4
IdentityServer4 是为ASP.NET Core 2.系列量身打造的一款基于 OpenID Connect 和 OAuth 2.0 认证框架。