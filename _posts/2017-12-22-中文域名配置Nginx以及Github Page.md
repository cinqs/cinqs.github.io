---
layout: post
title: 中文域名配置Github page以及Nginx VH
---

1. 需要现将你的域名转为 [`punycode`](https://www.punycoder.com/), 可以使用 https://www.punycoder.com/ 转化，也可以将你的域名粘贴在浏览器中（*Chrome* 比如说），然后可以在 *developer tools -> Network* 里面看到转换成的 *Punycode*

2. Github 可能会拒绝使用你的 Punycode 作为你的域名使用，没问题，你可以在你的 *repo* 下载建一个 **CNAME** 文件，然后将你的域名的 *Punycode* 贴进去

3. *Nginx* 就是在 *server_name* 后面贴上你的域名的 *Punycode*
