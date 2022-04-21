---
layout: post
title:  "洞见101之AI辅助测试与自动化测试"
date:   2021-06-20 00:00:01
categories: autotest update
---

### 介绍

随着AI的飞速发展，AI已经使用到许多软件系统中，也逐步的应用到软件测试的工作中，并且利用各种AI相关的技术来辅助测试工作也是未来一个测试趋势。当前AI的主要几个应用方向包括，视觉系统，语音系统，决策系统，自然语言处理系统和大数据系统，因此在测试辅助工作中其最主要的方面包括：基于业务流程、被测系统内容、测试用例和测试数据的学习，测试用例和测试数据的生成，自动化测试代码的生成，测试中的视觉识别和NLP，自动化探索性测试，大规模测试日志和结果分析，缺陷定位等等。其中视觉测试，测试日志和结果学习，以及缺陷定位都已经有比较成熟的商业产品，但是相应的成熟的开源系统还暂时没有。而通过相应的学习，并生成测试用例，测试数据和自动化测试代码则是AI辅助自动化测试最为重要的部分，虽然这部分也有一些商业和开源的框架和工具，比如EvoSuite，test.ai，recheck-web等，但是都还不是非常不成熟。因为这些AI辅助自动化测试框架和工具虽然使用了AI技术，可以较好的通过学习各种已知信息，比如业务流程图，已有的手动测试用例，测试日志等。然后通过深度学习，并生成新的测试用例和测试数据，甚至相应的自动化测试代码。

但是现在它们都存在不少问题，其中包括很难或者不能生成测试预言（Test Oracle）来验证测试结果；测试用例准确性和覆盖率不够高，无法替代当前人工设计的测试用例；生存的测试用例不易管理，比如难以理解与阅读，导致很难维护等。导致这些问题最主要的原因是现在AI算法本身存在问题：无法保证准确性。截止2021年，我参加过多个业界的软件和测试大会，其中有几个中国一线互联网厂商分享的AI自动化测试的准确率也只有80%多，不足90%。这种准确率在金融，工控等一些要求很高的系统中很难获得认同，并且需要专门的定制化从而很难通用，导致它们几乎都是自研自用，很少公开对外。而那些所谓通用的商用和开源的AI自动化测试工具则更难达到这样高的准确性，因此现在绝大部分AI自动化测试工具都只能作为常规测试的扩充，而不能替代现在已有的测试工作。

我将AI辅助自动化测试分为3个级别，只有达到第3个级别，并且准确性达到99%及以上，这样的AI辅助自动化测试才有可能替换高质量产品的功能回归测试。这3个级别分别是：

- L1，半自动化辅助级

  通过深度学习模型自动生成测试用例的输入，人工验证输出。

- L2，全自动化规则级

  通过深度学习模型自动生成测试用例的输入，并通过固定规则模型来自动验证输出。

- L3，全自动化模型级

  通过深度学习模型自动生成测试用例的输入和输出，并通过深度学习模型来自动验证输出。

现在业界很多公司基本上能实现级别1，因为无法全自动化，导致现在只是用来查漏补缺，辅助测试人员设计出更多并且更高覆盖率的测试用例。不过也有少量头部公司已经实现了级别2。其中级别2需要很高的定制化，实施成本较高，并且很难通用，导致很难大规模推广。而级别3遇到的最大问题还是深度学习的不准确性，导致测试预言的不准确，从而导致需要大量的人工审核工作，从而很难真正的落地实施。


而陈磊老师的[文章](https://mp.weixin.qq.com/s/ykOQAyYACTPzAOZgyIZFAw)中将AI测试框架分为6级，分别是：

- L0，原始级

  测试工程师还是在做测试用例设计、执行、回归、修复后再回归。没有专职的人写自动化的脚本。测试人员按需撰写脚本，遇到较大变更的时候还要检查脚本是否有效。

- L1，辅助级

  测试框架能帮助测试工程师完成一些枯燥乏味的工作，通过一些算法完善测试脚本并将测试结果发送给对应的工程师，由工程师来决策测试结果。

- L2，部分自动化级

  自动化测试的算法可以自我容错，不需要大量的维护工作，会按照测试用例去执行与识别，不会影响执行流程。然后它会把测试结果发送给测试工程师，由工程师决策测试。

- L3，有条件的自动化级

  测试工程师能建立自己的测试基线与准则，测试框架可以通过机器学习完成基线的建立，可以在无人干预的情况下完成测试，测试工程师只需要了解被测系统和数据规则即可，并自动的确定Bug。并且还可以收集并分析全部的测试用例，通过机器学习等相关技术，人工智能系统可以检测到变化中的异常， 并只将异常提交给人工进行验证。

- L4，高度自动化级

  系统能模拟类人的行为，进行并执行一些逻辑脚本或者业务脚本的撰写，达到一种完美的人机交互。它可以将测试结果定优先级，会根据严重程度发到测试管理系统，但不会对没有样本的做定义，还需要人类决策。

- L5，全量自动化级

  系统能够完成过程化的测试，了解产品的变更，知道产品的黄金流程，同时还会将问题完全反馈。测试工程师在这里仅仅做的是算法逻辑的维护、规则的维护。

这个分级更为精细，根据这个分级，现在大部分的自动化框架都是L1辅助级别，同时都有一部分已经达到L2，而很少能达到L3以及以后的级别。

### 总结

当前AI辅助自动化测试需要在两个方面有所突破，这样才能很好的达到全自动化模型级和全量自动化级，并且提高其准确性。它们分别是自然语言处理和大数据预测。首先对于自然语言，主要是用来对于业务需求，验收条件等被测系统的信息进行处理并学习，并真正的理解被测系统，从而提高测试用例和测试预言的准确性。其次是大数据预测，主要是开发准确性更好的大数据预测模型，通过学习到的被测系统的大量数据信息，提高测试预言预测的准确性。只有这两个方面有所突破，AI辅助自动化测试才能被真正的通用和普及，从而真正的可以替代当前人工测试分析，测试用例设计和自动化测试开发工作。纵观当前的AI技术，要实现这个目标还有相当长的路需要走。