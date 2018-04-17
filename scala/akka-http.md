AKKA-HTTP  翻译
源文档链接： https://doc.akka.io/docs/akka-http/current/security.html

# Introduction
Akka http 组件是基于akka-actor和akka-stream. server和client的http全栈实现。
它不是一个web框架，而是提供和消费Http-base的服务的更通用的工具箱。
但是与浏览器交互的时候却不是Akka Http的不是主要焦点

## 哲学理念
Akka Http 主要理念是为建立集成层(integration layers)提供工具而不是提供应用层核心。
就其本身而言，它是一个库而不是框架。

Framework是给你一个框架，在这个框架中你建立一个应用。 