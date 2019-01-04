---
layout: default
title:  "初识电报机器人(Telegram Bot)"
date:   2018-12-28 15:00:00 +0800
categories: Programing
---
### 什么是Telegram Bot
监听用户的消息，对处于资料库中的消息进行自动回复的特殊用户。注意：这个资料库不是设置在 Telegram 平台上，而是通过请求 API 在你的服务器上进行处理反馈。

### 如何获得一个Telegram Bot
* 在 Telegram 中找到 `BotFather`, 输入 `/help`,就会出现所有和 bot 有关的命令。
* 输入`/newbot`, 然后输入 name 和 username, BotFather会返回给你一个 token(这个 token 是接下来调用 API需要用到),这个机器人就成功创建了

### BotFather常用功能
* `/setcommands` 设置 Bot 的触发命令
* `/setinline` 设置 Bot 内联查询模式
* `/setjoingroups` 设置 Bot 是否可以被加入群组
* `/setprivacy` 设置 Bot 是否可以监听所有群组消息，还是只有@它的消息。注意: 如果 Bot 被设置为管理员会自动监听所有群组消息

### Token如何使用
Token 是用来通过调用 API 指导机器人去获取你想要的信息，然后给予回复。举个例子: 请求`https://api.telegram.org/bot<Token>/getMe`, 你就可以得到这个机器人的信息。 还有很多具体的方法和数据类型，参考 [Bot API](https://core.telegram.org/bots/api#available-methods)页面。

### 监听群组的两种方式
我们是需要实时监听群组里发送的信息，然后找到需要 Bot 的信息，完成处理再回复给群组中的用户。监听的方式有两种:
* get_updates 这种需要你手动每次去请求这个方法，然后接口会返回最近请求机器人帮助的信息，服务器解析完，挨个回复。
* webhook 机器人会实时将群组里的信息推送到你的服务器，你对信息进行解析，然后回复。

文档中推荐开发用get_updates这种方式，然后正式环境是用 webhook 这种方式。
webhook 方式需要你请求`/setWebhook` 去设置把信息推送到服务器哪个url,这个 url 必须是 https;另外还需要设置你想要监听的消息类型，用allowed_updates参数控制。

为了避免别人攻击你这个 url, 文档中建议你把 Bot Token 设置为路由。e.g. `https://www.example.com/token`。

### 用户与 Bot 的交互方式
主要有三种：
* 普通消息，结构体是`%{"message" => %{"text" => xxx}}`, 服务器需要对 text 进行解析，然后返回给他你想返回信息。
* command mode, 用户再群组输入框中直接输入/(还记得上面 BotFather 有个/setcommands 命令吗)，这时候会弹出已经设置好的命令列表，用户选择其中一个，然后这条信息会推送给服务器（结构体同上)，服务器解析 text 返回给用户相应的信息。
* inline mode ，输入框中输入 `@Bot 然后输入你的问题`, 这时候服务器会收到推送消息，消息体结构是`%{"inline_query" =>  %{"query" => 问题}}`，然后服务器在设定好的问题库中找出相关的答案，返回给用户，用户再选择他需要的那一条，答案会展示再界面上。推送消息的数据类型
