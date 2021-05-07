---
layout: post
title: URL转码中空格转码的问题
categories: Html
description: URL转码中空格有时转码为%20，有时候转码为+号
keywords: URLEncode, %20
---

> 为什么URL转码中空格有时转码为%20，有时候转码为+号？

答案：因为 [URI规范 RFC 2396](https://tools.ietf.org/html/rfc3986) 和 [W3C规范](https://www.w3.org/TR/html4/interact/forms.html#h-17.13.4.1) 冲突了。

- W3C规范中规定当Content-Type为application/x-www-form-urlencoded时，URL中查询参数名和参数值中空格要用加号+替代，所以几乎所有使用该规范的浏览器在表单提交后，URL查询参数中空格都会被编成加号+。
- URI规范中规定URI里的保留字符都需转义成%XX格式，因此空格会被编码成%20，+会被编码成%2B。



URI规范中我们首先看看 URI 中的[保留字](https://tools.ietf.org/html/rfc3986#section-2.2)，这些保留字不参与编码。保留字符一共有两大类：

```
reserved = gen-delims / sub-delims

gen-delims = ":" / "/" / "?" / "#" / "[" / "]" / "@"

sub-delims = "!" / "$" / "&" / "'" / "(" / ")"
                  / "*" / "+" / "," / ";" / "="
```

URI 的编码规则也很简单，先把非限定范围的字符转为 16 进制，然后前面加百分号。

- 空格这种不安全字符转为十六进制就是 0x20，前面再加上百分号 % 就是 %20。