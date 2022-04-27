---
layout: post
title:  "洞见101之自动化前端页面性能测试介绍"
date:   2021-05-20 00:00:01
categories: performance update
---

###  介绍
随着 Web 应用的空前发展，前端业务逐渐复杂，为了处理这些复杂业务，前后端分离，出现了专门应对这种分离架构的应用开发框架，比如 Angular，React，Vue 等，从而也导致 Web 应用的复杂度大大增加，并出现了 SPA（Single Page web Application）。同时随着越来越多的用户使用移动设备访问 Web 应用，使得 Web 应用需要支持一些性能并不是很好的移动设备。为了度量和测试 Web 应用是不是在高复杂度的情况下，页面性能能满足用户的需求。前端页面性能测试本质上和本地应用性能测试类似，其性能和运行应用的设备的性能强相关，即运行被测系统的硬件性能越强，性能也越强。所以测试前端页面性能需要找到一个固定配置的硬件来测试其性能基线，然后通过这个基线推测或者计算其他硬件配置下的性能情况。其中在项目开发的过程中持续对一个固定配置的硬件进行性能测试，也可以判断性能在开发过程中趋势，从而第一时间发现性能问题。而测试性能基线，一般是选用最为普通和常规的配置，而不能选用最为流行的高性能配置，不然得到的基线很容易让测试人员有一种页面性能很高的误解。前端页面性能一般都是有专业的性能工具来做，比如免费的 WebPageTest、PageSpeed Insights、SiteSpeed 等，这些功能都能检测前端页面的各种性能指标，但是这些工具要么只提供基于在线服务，导致安全性无法保证；要么安装和配置比较复杂或者无法自动执行，导致很难和持续构建流水线集成。Google 开发的免费的 Lighthouse，很好地解决了以上的问题。

### Lighthouse
Lighthouse 是 Google 开发的一款分析 Web 应用和 Web 页面的性能的工具。除了性能，它还可以分析 Web 页面的 Accessibility，各种页面最佳实践（Best Practices），SEO 以及 Progressive Web App 的能力，对它们打分，并展示出每一项基础项目的数据和结果，如下图：
![img](http://twranman.github.io/assets/autotestforwebpage/1.png)

其中对于性能的分数，它是根据 6 个不同的性能指标计算而得到的，如下图：![img](http://twranman.github.io/assets/autotestforwebpage/2.png)
而这 6 个性能指标又来于 Chrome DevTool 中的 Performance，点击上图中的“View Original Trace”可以跳转到 Performance，见下图：

![img](http://twranman.github.io/assets/autotestforwebpage/3.png)
所以，Lighthouse 中的 Performance 所计算的分数是真实性能分数，通过这个分数可以了解到这个页面整体的性能情况。由于 Lighthouse 除了支持通过 Chrome DevTool 和 Chrome Extension 的手动的方式来使用以外，还可以通过 Node CLI 和 Node Module 的方式来使用。这种方式特别适合在集成流水线中使用，用以持续测试前端页面的性能，并检测页面性能随着开发而产生的变化趋势，从而尽早地发现前端页面的性能问题。

### Cypress和Lighthouse
实施前端页面自动化性能测试最好的方式，就是将它嵌入 Web App 的功能或者端对端自动化测试过程中，当功能和端对端自动化测试执行的过程中就执行了自动化性能测试。Cypress 是当前最为流行的 Web 自动化测试框架之一，它有一款插件 Cypress-Audit 就很好地集成了 Lighthouse 的功能。它能在 Cypress 的自动化测试运行的过程中，针对每张测试过的页面生成 Lighthouse 的性能分数，并展示在 Cypress 的测试报告中。而且还可以针对这些分数做断言，使得当某个页面的分数低于某个阈值的时候，持续构建流水线就会中断，从而通知开发人员或者测试人员对其进行性能分析，从而发现可能存在的性能问题。首先需要在 Cypress 的自动化测试代码中，配置这 6 个性能指标的阈值，比如配置 First Contentful Paint 的阈值时间为 1000 毫秒，配置代码如下:
![img](http://twranman.github.io/assets/autotestforwebpage/4.png)
然后在执行 Cypress 自动化测试的过程中，某个页面的指标不满足配置好的阈值，比如 First Contentful Paint 的时间超过了 1000 毫秒，测试就会失败，其测试报告如下图：

![img](http://twranman.github.io/assets/autotestforwebpage/5.png)
然后可以手动在使用 Chrome 浏览器中的 LightHouse 和 Performance DevTools 去分析其性能问题，并通过优化将这个 First Contentful Paint 的时间降到 1000 毫秒以下，其测试就会通过。或者通过分析得知无法提高这个指标，将阈值时间改到 2000 毫米，测试也会通过。

### 总结
随着页面越来越复杂，前端页面的性能测试势在必行。其次随着页面规模越来越大和持续迭代交付的流行，前端页面自动化性能测试一定能节约大量人力成本，并且还能尽早地发现一些性能问题。最后还需要将其嵌入到前端 Web 应用的功能测试中，从而可以节省其开发成本，并且可以容易地在持续集成流水线上执行，最终实现持续前端页面性能测试。