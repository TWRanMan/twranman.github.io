---
layout: post
title:  "自动化测试框架Cucumber和RobotFramework的实战对比"
date:   2015-04-01 13:50:26
categories: autotest update
---

**本文首发表于InfoQ：[http://www.infoq.com/cn/articles/cucumber-robotframework-comparison](http://www.infoq.com/cn/articles/cucumber-robotframework-comparison)**

## 一、摘要

自动化测试可以快速自动完成大量测试用例，节约巨大的人工测试成本；同时它需要拥有专业开发技能的人才能完成开发，且需要大量时间进行维护（在需求经常变化的情况下），所以大部分具有很好开发技能的人员不是很愿意编写自动化用例。但由于软件规模的高速增长，人力资源的逐步稀缺，自动化测试已是势在必行。

对于自动化测试首先需要保证其功能是对客户有价值的和正确可用的。而这一切的基础就是用例要能测试客户的需求，期望，最好能让客户参与到测试用例的开发过程中来或让客户评审测试用例，因此出现了ATDD、BDD等各种理论方法来支撑这一行为。现有很多自动化测试工具可支持ATDD、BDD等，比如Cucumber 1 、RobotFramework 2 、SpecFlow 3 、JBehave 4 、Fitness 5 、Concordion 6 等。其中Cucumber和RobotFramework是最流行的两个框架，但许多人在第一次选择测试框架时因缺乏实践经验而困惑，所以今天为大家分享这两款框架在几个项目上的经验及对比，方便大家在以后的项目上能正确地选择这两款测试框架。

首先看一下这两款工具的简单对比。

|              | Cucumber                    | RobotFramework                   |
| ------------ | --------------------------- | -------------------------------- |
| 编程语言支持 | Java,Ruby,JavaScript etc. 7 | Python, Java, C                  |
| 支持的系统   | 所有主流系统                | 所有主流系统                     |
| 主要的IDE    | IntelliJ,RubyMine, Eclipse  | RIDE, IntelliJ, PyCharm, Eclipse |
| 费用         | 免费                        | 免费                             |
| 多国语言支持 | 用例层面支持UTF-8           | 用户关键字及用例层面支持UTF-8    |

## 二、案例

#### Cucumber案例1：某社交网络系统

**项目时间**：4年前

**项目背景**：系统的主要功能是帮助用户能通过一个手机应用同时与Facebook，Twitter，Flickr等社交网络更新信息，并能一次性把自己更新的信息同步到这些社交网络。其中它有一个服务器端，用于和各个社交网络通信，一个Web应用和一个手机应用提供给最终客户使用。它的技术栈主要是Java Spring，Android，iOS，MySQL等。

**被测系统构架图**：

![img](http://twranman.github.io/assets/CucumberRobotFramework.resources/E6478503-3B0F-46F1-81DF-4B59E12480B5.png)

由于这个项目是中国团队和法国团队一起合作开发，当时法国团队的架构师提出选用Cucumber作为自动化测试框架来测试这个系统，项目需要支持多国语言，且需要同时做服务器和手机端的功能测试，甚至在一个测试场景中既包含服务器测试部分，又含手机端测试部分，而使用基于Cucumber的测试系统很好的满足了我们的需求，其中手机端的功能测试用的是Calabash 8 。Calabash是一个手机功能测试系统，它使用Cucumber将Android的测试框架Robotium 9 和iOS的测试框架Frank 10 封装了起来，使得Cucumber的Step可以调用Robotium和Frank进行测试。这样就可以实现一个测试场景里面既包含手机端测试，又包含服务器端测试，比如：

I "submit" update to "Facebook" with "I am happy today" on "Android"

I "get" update on "Facebook” with "I am happy today" on "Server"

实现方式是在Calabash中使用Ruby实现一层胶水代码，和服务器测试功能测试代码连结起来，并根据不同的Step调用不同的测试驱动层代码从而实现同一个测试用例同时包含服务器端和手机端测试。虽然这样的测试用例不会很多，但它却有效的表达了端到端的系统集成测试，让测试集合更加丰满。

如果重新选择测试工具，我还是会选择Cucumber和Calabash，主要原因是它们可以方便的统一做手机和服务器的功能测试。虽然RobotFramework配合Selenium也能实现类似的功能，但是需要使用RobotFramework对Selenium重新进行封装，没有Calabash方便易用。

#### Cucumber案例2：某大型养老保险系统

**项目时间**：2年前

**项目背景**，主要功能是提供一个Web系统让用户可以购买养老保险，管理养老保险账户里面的资金等业务。主要的技术栈Java Spring, JSP, AngularJS, Oracle DB等。

**被测系统构架图：**

![img](http://twranman.github.io/assets/CucumberRobotFramework.resources/7E4E36AB-A841-45B4-9885-70305576C5ED.png)

基于安全和开发成本原因，比如重用已有的服务器和容器环境，重用开发资源，所以公司绝大部分项目只用Java语言进行后台服务器端开发，导致公司大部分人员只熟悉Java语言，因此测试框架选择了Cucumber Java版 11 。

如果重新选择工具，由于技术栈和成本的原因，我仍然会选择Cucumber Java版，不会考虑RobotFramework。因为对于这种Java Spring商业应用项目，我不想引入一个Jython去加深项目的技术栈，只要能充分利用当前团队已有的技术栈就可以了，并且还更容易说服开发人员帮忙实现和维护自动化测试，从而促使整个团队都能对自动化测试负责。

#### RobotFramework案例1：某AC项目

**项目时间**：3年前

**项目背景**：该项目是WIFI系统的AC（Access Controller 接入控制器）部分，包含WIFI接入的认证、计费等功能。它也提供了配置界面，包括Web和命令行两种。AP（Access Point接入点）是与该系统交互的外部系统。通常来说AP会有很多个，放置在不同的空间区域，提供WIFI接入服务，AP和AC之间使用有线链路连接。

**被测系统构架图：**

![img](http://twranman.github.io/assets/CucumberRobotFramework.resources/7E1219B4-D1FE-452D-ADD8-2B7F742F493B.png)

该系统作为一个嵌入式设备，从用户的角度来看主要包括两部分功能。第一部分是操作管理员在命令行或者Web界面上进行功能配置，第二部分是AP与系统进行交互，完成网络接入等功能。

明确了被测对象和场景后，就需要寻找相应的测试库来完成这些用户（即包括人，也包AP）与系统之间的交互。对于Web来说，有成熟的Selenium可以使用，Selenium提供了多种语言的API **，** 从这个角度来看RobotFramework和Cucumber都可以选择。对于命令行操作而言，可以选用RoboFramework的SSH库来完成，当然在这一点上其他的语言也有相应的类库。要想完成上述这个系统的测试，还需要完成报文的收发及编解码工作，Python的类库Scapy 12 能够很好地完成这部分工作，只需要在此之上做少量定制化开发，并将其封装成为RobotFramework关键字即可。经过上面的分析可以看到，使用基于Python的RobotFramework能够很好地处理报文相关的逻辑，加上团队在Python上有比较好的技术储备，因此RobotFramework成了最终的选择。

如果重新选择，我还是会选择RobotFramework，原因是其他平台上找不到类似Scapy这样好用的测试库。

#### RobotFramework案例2：某移动广告管理平台

**项目时间**：1年前

**项目背景**：该项目是一个Web系统，用于广告投放、查询、显示等功能。

测试思路是做端到端的测试，覆盖从广告投放、广告查询及广告显示等一系列功能。其中涉及到的测试库主要是Selenium，这点上与案例1类似。不同之处在于这个项目中参与自动化用例编写的主要是从不编写代码的测试人员，而RobotFramework有一个专用的用例编写环境—RIDE，其中用例编辑窗口如下图：

![img](http://twranman.github.io/assets/CucumberRobotFramework.resources/6C21D981-D3F4-47DA-BA8D-4DDE6E6872EA.png)

虽然它只是简单地把使用TAB符号隔开的一系列纯文本变成了可视的表格，但对于这些测试人员来说，他们以前工作的平台就是Excel中，所以很容易切换过来。再加上它提供的一些高亮、抽取关键字等特性，使得测试人员可以比较专注于测试用例的设计、编写和优化，而不用关心格式等细节问题。

在RIDE中导入相关测试库之后，可以通过F5快捷键查看所有关键字的文档，如下图所示：

![img](http://twranman.github.io/assets/CucumberRobotFramework.resources/E56778B5-E347-402A-AA82-DFA7394F6591.png)

关键字的概念很有趣，它们本质上就是一堆自由函数，或者是类的成员函数 13 ，所以使用它们来编写用例事实上就是在编写代码，本质上和Cucumber的Step Definition是一样的。但由于RIDE以表格的形式呈现，并且有良好可视化的文档，在这种环境下写测试会给人一种“我不需要编写代码”或“我没在写代码”的感觉。在我们经历过的几个项目中，测试人员对这种形式都比较接受。当然从另一个角度看，用例的编写者失去了对测试代码的直接掌控权，这对于很多开发人员来说还是有些别扭，所以如果你不喜欢RIDE这种形式，可以尝试使用Pycharm来开发RobotFramework用例。

在这个项目中有不止一套测试运行环境，比如开发集成环境、CI环境、系统测试环境等。该项目包含了多个Web Portal，每套环境中Web Portal的IP地址都是不同的。如何保证一套测试代码能够在不同的环境中无差别的运行呢？简单的答案是配置文件或者环境变量，在RobotFramework中，解决方案是变量文件。比如我希望测试代码能够在开发集成环境和CI环境中运行，则可以按照下面的方式操作。

首先定义两个变量文件：

**ci-env.py:**
`portal_ip = “192.168.1.1"`
`……`

`dev-share-env.py:`
`portal_ip = “192.168.1.4"`
`……`

在用例文件中可以按照下面的方式引用上述变量文件中的变量：

`……`
`**open** browser ${portal_ip}`
`……`

然后在运行测试时加入如下的命令行参数即可针对CI环境运行测试：

`pybot -V ci-env.py tests/`

开发人员在自己编写调试测试或者定位问题时，在命令行中使用dev-share-env.py的配置即可针对另一套环境进行测试。

在这个项目中遇到的另一个问题是中文问题。客户非常在意用例是否能很好地处理中文，一方面是因为可读性，另一方面是要适配一些使用中文编写的Java代码。RobotFramework和Cucumber这些工具都有考虑国际化的问题，比如Cucumber Java版就支持使用类似于“给定”、“当”等中文关键字及中文的Step Definition，而RobotFramework对中文的支持也很好。但是如果从可用性的角度考虑，RobotFramework会比Cucumber好一些。原因是Cucumber本身并没有专用的IDE，只能求助于其它IDE插件，这些插件可以完成高亮、自动补全和Step Definition跳转等功能，但一旦使用了中文，有些功能就不好用了。而在RIDE中就不存在这个问题。所以如果你的团队因为某种原因对于中文用例的需求很旺盛，可以考虑RobotFramework。

如果重新选择，我还是会选择RobotFramework，主要从两个方面进行考虑：一方面是因为其“不用写代码”的方式更容易被测试人员接受，另一方面是对中文的支持更好。

通过上面四个案例，展示了在不同的项目中实际使用Cucumber和RobotFramework时的一些经验和选择它们的理由。但对于Cucumber和RobotFramework更多的知识点，下面有一个更为详细的对比表。

|             | Cucumber                                                     | RobotFramework                                               |
| ----------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 亮点        | 使用自然语言，更易读支持表格参数  14支持多种格式的Report：html、junit etc.支持多种语言支持四种状态的测试步骤  15  ：Passed、Failed、Skipped、Pending支持使用变形器消除重复  16一个商用的在线Cucumber系统：Cucumber Pro  17 | 使用关键字的机制，更容易上手提供了RIDE，对于不熟悉编码的人来说比较友好能够精细的控制关键字的scope  19Log和Report非常好  20使用变量文件的机制来描述不同的环境  21丰富的关键字库  22内置变量  23 |
| 不足        | 缺乏类似RIDE对纯测试人员友好的专用工具                       | 不支持类似于Cucumber中的表格参数  14使用Jython版本测试运行启动慢 |
| Jenkins支持 | 支持                                                         | 支持                                                         |
| Maven支持   | 支持                                                         | 支持                                                         |
| 开发效率    | 高效-Jetbrains系列的IDE                                      | 高效-RIDE和Jetbrains系列的IDE                                |
| 商业支持    | 支持 18                                                      | 无                                                           |
| 社区支持    | 广泛                                                         | 广泛                                                         |



## 三、测试工具选择的一般性原则

在上述的实战案例中，针对项目的具体情况我们对Cucumber和RobotFramework这两种工具进行了取舍。本节就来总结一下这些取舍的考虑因素。

首先来看一下自动化验收测试的层次：

![img](http://twranman.github.io/assets/CucumberRobotFramework.resources/AE78DAC9-B7C6-4BA1-A57E-1AA0C363CA01.png)

然后对每层进行分析：

1. 最下面是被测系统，需要明确它的形态，比如是Web系统、REST系统或者特定协议系统。

2. 中间是测试库。比如Selenium、SSH、Scapy等，有了它们用例才能和被测系统进行交互，所以需要根据被测系统的形态来选择相应地测试库。该层的选择需要考虑几个因素：

   * 测试库的易用程度。

   - 测试库是否有良好的商业或者开源社区的支持。
   - 团队成员是否熟悉测试库使用的编程语言。

3. 最上层则是测试框架，也就是Cucumber和RobotFramework这一层。其作用包括用例管理、测试数据管理、测试运行、测试报告等。该层的选择需要考虑几个因素：

   - 这一层会通过函数调用的方式和测试库打交道，因此测试框架必须支持测试库所使用的编程语言。
   - 是否提供易用的测试用例开发环境，比如是否有如RIDE这样的专用工具，或者Intellij的IDE的插件。
   - 引入某个测试框架之后对现有工作模式的影响程度，比如让不懂编程的测试人员写代码。

以上这些点是从RobotFramework和Cucumber的对比中总结出来的，但如果你想要选择这两者之外的其他框架，同样可以考虑上述这些原则。

## 四、总结

我们在银行、保险、社交、电信、物流、互联网等项目上实施过自动化功能以及验收测试。对于Cucumber和RobotFramework到底属于ATDD还是BDD，这里不做过多的讨论，因为当前在业界对于ATDD和BDD怎么区分还有一些争议，而对于我们来讲，它们只不过是两个名词而已。对于这两个工具本身来讲，只需要清楚它们各自的特点，根据项目本身的情况和需求选择就可以了，简单地说就是：知行合一。

1. ***http://cukes.info/***
2. ***http://robotframework.org/***
3. ***http://www.specflow.org/***
4. ***http://jbehave.org/***
5. ***http://www.fitnesse.org/***
6. ***http://concordion.org/***
7. ***http://cukes.info/platforms.html***
8. ***https://github.com/calabash***
9. ***https://code.google.com/p/robotium/***
10. ***http://www.testingwithfrank.com/***
11. ***https://github.com/cucumber/cucumber-jvm***
12. ***http://secdev.org/projects/scapy/***
13. ***http://robotframework.org/robotframework/latest/RobotFrameworkUserGuide.html#creating-test-libraries***
14. ***https://cukes.info/step-definitions.html#data_tables***
15. ***https://github.com/cucumber/cucumber/wiki/Step-Definitions***
16. ***https://github.com/cucumber/cucumber/wiki/Step-Argument-Transforms***
17. ***https://cucumber.pro/***
18. ***http://cukes.info/support***
19. ***http://robotframework.org/robotframework/latest/RobotFrameworkUserGuide.html#test-library-scope***
20. ***http://robotframework.org/robotframework/latest/RobotFrameworkUserGuide.html#report-file***
21. ***http://robotframework.org/robotframework/latest/RobotFrameworkUserGuide.html#variable-files***
22. ***http://robotframework.org/#test-libraries***
23. ***http://robotframework.org/robotframework/latest/RobotFrameworkUserGuide.html#built-in-variables***