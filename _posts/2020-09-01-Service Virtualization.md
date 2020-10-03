---
layout: post
title:  "软件测试中的服务虚拟化"
date:  2020-09-01 00:00:01
categories:  test service
---

## 问题

最近几年虽然微服务十分火热，但是仍然有不少人不喜欢微服务，甚至抵制它。其中最主要的原因就是其成本高，难度大。对于难度大，主要是遇到了一些不容易解决的问题，而在这些问题中，其中包括以下三个和测试数据以及测试环境相关的问题：

### 问题一：测试环境被多个团队共同使用

在大规模的微服务系统中，某些核心服务很多时候都是会被多个团队在共同调用，并且它可能也有多个依赖服务。 而当一个服务的某个测试环境被多个团队（服务）共同使用的时候，主要会存在以下两个困难点。

1、同一测试数据可能会被不同的团队修改。有些团队通过创建多套测试环境来解决这个问题，但是这样的成本很高。对于很多技术强大的互联网公司，可以通过Docker等技术手段来降低一些成本，但是对于很多传统企业来讲，高成本的多环境很难实施。 

2、同一测试数据可能被其他团队占用，所谓的占用就是一个测试数据一旦不小心被某个人使用了，他可能按自己的场景在进行使用，这个时候你去用它，很可能受到影响而得不到自己想要的结果。

### 问题二：测试数据准备需要花费大量时间

当测试一些业务不是很复杂的系统时，准备测试数据也许不是一件困难的事情。但是在一些传统行业的复杂系统中，准备测试数据是一项非常困难的事情，比如在银行，保险，通信等复杂系统中。我曾经测试过一个保险系统，要在测试环境中准备一套数据甚至需要几个小时，因为整个系统的业务非常复杂，数据库设计也非常复杂，而且还是遗留系统，几乎没有人懂得直接操作数据库来准备数据。所以准备数据就必须系统本身来创建。而系统本身是基于MainFrame的，而且UI全部是Console下的UI，操作十分繁琐和复杂，导致创建一套测试数据需要花费很长时间。很多银行和保险公司的核心系统到现在也是保留这样的模式。 因此在这样的传统行业中的遗留系统中，测试数据的准备是一个非常大的问题， 
其次很多系统中，测试数据一旦使用了，状态就会改变，从而不能重复使用。所以再次测试就需要重新创建测试数据，这也是一个常见的严重的问题。

### 问题三：服务部署或网络等问题导致测试环境不稳定以及版本不匹配

这个也是经常会遇到的情况。对于一些稳定而没有什么变化的系统，也许这不是一个问题，但是对于一些正在开发过程中，或者有大量修改或者本身不稳定的系统中，这个问题就十分常见。某些服务部署和网络问题，这个容易理解了，就是依赖的服务正在部署。其次是依赖服务的正在调试，而调试的过程中，服务本身的一些状态可能在不停的改变。 或者依赖服务存在Bug，导致服务也存在问题。 最后也许消费端只需要版本1.0依赖服务，但是测试环境中已经部署了2.0版本的服务，导致服务对消费端来讲也不可用了。

![image-20200913203609414](/Users/rliu/Library/Application Support/typora-user-images/image-20200913203609414.png)

### 解决方案：服务虚拟化

可以使用服务虚拟化（Service Virtualization）技术来解决以上这些问题。下图是服务虚拟化的简单示意图：

![image-20200913203840000](/Users/rliu/Library/Application Support/typora-user-images/image-20200913203840000.png)

服务虚拟化看起来虽然简单，但是其实现已经做到非常丰富的功能，比如Hoverfly等，从而解决上面那一系列问题。

## Hoverfly

Hoverfly是一个开源免费（Apache 2）的服务虚拟化的一个工具，其虚拟数据是可以复用的Json格式的Simulation。它是基于 Go开发的，轻巧，高效。同时支持Python和Java进行扩展，也提供REST API来对其进行控制。并且暂时提供模拟网络延迟，随机错误和限定速率。但是其支持的协议有限，暂时只支持HTTP和HTTPS。但是其最重要的是其支持六种工作模型，它们分别是：Capture模型，Simulate模型，Spy模型，Synthesize模型，Modify模型，Diff模型。

通过这六种模型，基本可以实现服务虚拟化的各种功能。首先，通过Capture模型可以获取到在手工测试和系统正常使用的情况下，各种服务的交互数据，然后再进行分析和修改，可以获得更多类型的数据。

将这些数据通过Spy、Synthesize、Modify和Simulate模型进行不同类型的服务虚拟。不同的团队可以根据基础类型数据快速定制自己团队的私有虚拟数据集，并且还可以根据不同版本的服务，定制不同版本的虚拟数据集，从而隔离了不同版本服务之间的数据，避免了不同团队之间的的测试数据冲突。

 

下面就来逐个介绍一下这六个模型。

### 1、Capture模型

![image-20200913205153998](/Users/rliu/Library/Application Support/typora-user-images/image-20200913205153998.png)

Capture模型是标准的录制功能。这个时候Hoverfly就是一个标准的Proxy服务。把它架设在被测的服务和外部服务之间，都可以把所有的交互数据录制成特定的Json文件，称之为Simulation。然后就可以根据不同的需求更改Simulation，并用到其他模型里面。 



### 2、Simulate模型

![image-20200913205147805](/Users/rliu/Library/Application Support/typora-user-images/image-20200913205147805.png)

Simulate是标准的Stub模型。可以将通过Capture模型录制到的Simulation或者手动编写的Simulation直接加载进Hoverfly，然后所有满足Simulation里面的匹配规则的Request就会返回Simulation里面的虚拟Response，不然就不会返回任何正确的Response。 



### 3、Spy模型

![image-20200913205139895](/Users/rliu/Library/Application Support/typora-user-images/image-20200913205139895.png)

Spy模型是一个最为特殊的模型，也是我在正式项目中最常用的一种模型。它最特殊的功能就是可以让部分请求获得虚拟的Response，而其他部分请求而获得真实的Response。 

因此，通过改变测试数据本身，可以确定使用的是实际服务还是虚拟服务。而这种情况下，在传统已有的这种Stub工具里面可能是需要自己手工去实现的，至少它默认不支持。但是Hoverfly是默认就支持，所以只需要把你的规则加到Hoverfly的这个虚拟文件里面，它就能实现这个功能。 

其次有些时候在同一个测试环境里面，我既需要虚拟的数据，又要真实数据的情况下，SPY模型是最佳解决方案。因为只有这样才能以最低的成本，来既实现了又要测真实的数据，又要测虚拟的数据。

为什么会需要这个模型？因为不少时候测试环境不稳定，大规模的回归测试不能依赖于外部服务，所以其需要依赖于虚拟的数据，但是依然要测一些外部的真实服务，为什么呢？因为担心外部服务的升级怎么办呢？那可能升级之后，服务的Request和Response的Schema改了，就会产生一个Bug。 
现实中会把一些所谓的重要的场景，依然是跑真实的外部服务，这样就算它的外部服务的Schema更改了，测试就可以在第一时间发现这个更改。

所以，这是一个最为经典和实用的模型。 



### 4、Synthesize模型

![image-20200913205132876](/Users/rliu/Library/Application Support/typora-user-images/image-20200913205132876.png)

Synthesize模型最主要解决的一个需求是根据不同的情况返回动态的Response数据。 

所以，这个时候其实是Hoverfly提供一种中间插件的功能，可以开发一个基于Pythong或者Java的中间插件，当某个Request收到之后，可以对这个Request进行加密解密，进行一个特定的判断，之后再返回一个特定的Response。 

可以对Request进行判断、处理，对Response进行一些特定的组合，然后再返回。



### 5、Modify模型

![image-20200913205045160](/Users/rliu/Library/Application Support/typora-user-images/image-20200913205045160.png)

Modify模型要等待真正的 Request收到后，才能将 Request转换为特定的内容，并发送给外部依赖，而外部依赖返回到 Response，再返回到特定的模式，以实现特定状态的虚拟。

比如需要模拟一个Request里面被加了某些东西，或者是做一些混淆，产生一些特定的用例，或者更改真实的Response中的某些数据等。 



### 6、Diff模型

![image-20200913205037993](/Users/rliu/Library/Application Support/typora-user-images/image-20200913205037993.png)

Diff模型相当于一个变异版的契约测试方案。首先当被测系统的API的时候，它会将依赖服务的返回数据保存起来。当下一次执行相同的测试用例的时候，它会把上一次的和本一次的依赖服务返回的数据的进行比较。

如果下一次的和第一次存储的的Schema有不一样的，那这个时候Hoverfly会报警，并显示出来两次不一样的格式。 这个就相当于就做了一部分被测系统和依赖系统之间的一种被动的契约的检查，虽然它不属于一个完整契约测试方案，但是至少做了单边的契约检查。



### 为什么要选择Hoverfly？

首先服务虚拟中心化，它是基于Proxy模型的，所以在它只要加一台机器搭建一个代理服务。 其次它是非侵入式的，它是不需要改动被测系统的代码或者配置的，而只需要改动JVM自己的property或者是操作系统的Proxy配置。 然后其灵活性很高，它支持各种模型，使用非常方便。 最后它是开源软件，所以如果有特定的定制化需求，可以根据自己的需求对其进行二次定制化开发。



## 总结

随着传统Stub和Mock服务技术的发展，以及微服务系统开发中对于服务测试的各种问题和需求，服务虚拟化孕育而生。服务虚拟化是对Stub以及Mock技术的提升和系统化，功能更为强大，从而可以更加容易使用和定制化，以便满足服务测试的各种新需求，解决各种新出现的问题。