---
title: "httpie - 人性化 HTTP Client"
weight: 2
---

# 人性化 HTTP Client 命令 - httpie

快速调试 HTTP Web 接口，你首先想到的方案是什么？或许是是图形化能力超强的 Postman，亦或是命令行工具 curl，甚至可能通过浏览器即可。

[httpie](https://github.com/httpie/cli/) 是一款更人性化的 HTTP 命令行客户端，简单来说，比 curl 更加易于使用。

## 安装

```zsh
brew install httpie
```

## 快手上手

最简单的使用案例，快速发起 GET 和 POST 请求。

GET 请求：
```
http GET http://httpbin.org/get name==poloxue age==18
```

POST 请求：
```
http POST http://httpbin.org/post name=poloxue age=18
```

http 命令的完整语法，如下所示：

```zsh
http [flags] [METHOD] URL [ITEM [ITEM]]
```

## 体现易用性

从易用性角度，如果拷贝一个 url 可直接通过在 scheme 之后添加一个 \<space\> 便可直接使用：

例如，GET 方法请求 http://httpbin.org/get，如下所示：

```zsh
http ://httpbin.org/get
```

演示效果：

![](https://cdn.jsdelivr.net/gh/poloxue/images@2023-11/2023-11-02-high-productivity-shell-commands-part3-06.gif)

另外，METHOD 默认也可省略的，省略规则是：

- 当有 data：POST，如 `http ://httpbin.org/post name=poloxue age=18`
- 当无 data：GET，如 `http ://httpbin.org/get`

httpie 还支持一种 offline 模式，debug 神器，只打印 HTTP 请求文本，但不进行网络请求；

```bash
http --offline ://httpbin.org/get
```

输出内容：
```bash
GET /get HTTP/1.1
Accept: */*
Accept-Encoding: gzip, deflate
Connection: keep-alive
Host: httpbin.org
User-Agent: HTTPie/3.2.2
```

现在，通过 offline 快速了解 httpie 的 header 设置，query 参数、JSON 请求体 等；

几个规则，如下所示：

- header：使用 `key:value` 设置 header；
- query string，使用 `key==value`，表示 query params，快速拼接 query string；
- body json data，使用 `key=value` 即可，更复杂结构，可通过命令管道或文件导入；

一个命令演示下如何设置：

```
http --offline POST http://httpbin.org/post \
                    X-API-Token:123456\
                    User-Agent:foo\
                    name==poloxue\
                    age==18\
                    password=123456\
                    email=poloxue123@gmail.com
```

启用 offline 调试模式，输出结果：

```bash
POST /post?name=poloxue&age=18 HTTP/1.1
Accept: application/json, */*;q=0.5
Accept-Encoding: gzip, deflate
Connection: keep-alive
Content-Length: 55
Content-Type: application/json
Host: httpbin.org
User-Agent: foo
X-API-Token: 123456

{
    "email": "poloxue123@gmail.com",
    "password": "123456"
}
```

## 输出美化

httpie 还有一个能力，它默认会对返回数据进行格式化与语法高亮处理。如此一来，其他的 json 格式化命令就用不到了，如早前介绍的 `pp_json` 这个命令。毕竟，httpie 的格式美化能力更强大。

想修改它的配色风格？

通过 `--style` 选项即可修改。


```bash
http --style autumn GET ://httpbin.org/get
```

如果想查看所有可用该配色，直接输入 `http --style` 即可查看：

输出内容如下：

```bash
usage:
    http -s/--style {abap, algol, algol_nu, arduino, auto, autumn, borland, bw, colorful, default, dracula, emacs,
friendly, friendly_grayscale, fruity, github-dark, gruvbox-dark, gruvbox-light, igor, inkpot, lightbulb, lilypond,
lovelace, manni, material, monokai, murphy, native, nord, nord-darker, one-dark, paraiso-dark, paraiso-light,
pastie, perldoc, pie, pie-dark, pie-light, rainbow_dash, rrt, sas, solarized, solarized-dark, solarized-light,
staroffice, stata, stata-dark, stata-light, tango, trac, vim, vs, xcode, zenburn} [METHOD] URL [REQUEST_ITEM ...]

error:
    argument --style/-s: expected one argument

for more information:
    run 'http --help' or visit https://httpie.io/docs/cli
```

httpie 的格式方式也是可配置的，选项 `--pretty`，可配置值有 `all|colors|format|none`，看名字大概能猜出它们的区别：

- all，默认值，即高亮又格式化；
- format，不高亮但格式化；
- colors，高亮但不格式化；

```
http --style=autumn ://httpbin.org/get
```

演示效果：

![](https://cdn.jsdelivr.net/gh/poloxue/images@2023-11/2023-11-02-high-productivity-shell-commands-part3-07.png)

## 文件下载

通过 `--donwload` 即可：

```zsh
http --download https://github.com/httpie/cli/archive/master.tar.gz
```

文件下载，除去最基本的需求，肯定还是要支持断点续传的。


要支持断点续传的话，首先，要通过 `--output/-o` 选项指定输出的文件名称。

```bash
http --donwload -o httpie.tar.gz https://github.com/httpie/cli/archive/master.tar.gz
```

断点续传，要启用 `--continue/-c` 选项。

为了掩饰效果，选择一个大文件进行下载，下载飞书的海外版 Mac 安装包。

命令如下：

```zsh
http -dco lark.dmg https://sf16-va.larksuitecdn.com/obj/lark-artifact-storage/49f5b75a/Lark-darwin_x64-6.11.16-signed.dmg
```

演示效果，如下所示：

![](https://cdn.jsdelivr.net/gh/poloxue/images@2023-11/2023-11-02-high-productivity-shell-commands-part3-08.gif)

httpie 就介绍这么多，其他还有 cookie、session，authentication 等等请自行查阅文档 [httpie 文档](https://httpie.io/docs/cli/)。

