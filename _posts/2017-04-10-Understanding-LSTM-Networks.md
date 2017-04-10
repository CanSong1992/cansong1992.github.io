---
layout: post
title: 理解LSTM网络
---
<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

原文：[Understanding LSTM Networks](http://colah.github.io/posts/2015-08-Understanding-LSTMs/)
## 循环神经网络
&emsp;&emsp;人类每一次思考不都是从头开始的。当你读这篇文章的时候，你读的每一个词都是基于以前每一个词的理解。所有的记忆都不会丢掉从头开始思考，思维都保存下来了。

&emsp;&emsp;传统的神经网络不能做到这些，他有一个主要的缺陷。例如，想象一下，你想要把一场电影中每一个点正在发生的事搞清楚。传统的神经网络很难从电影先前的事件从推断出后来的事情。

&emsp;&emsp;循环神经网络解决了这个问题。这种神经网络带有环，能够保存信息。

<div align = "center" style="margin-left:20%;margin-right:20%;padding:20px 25% 20px 25%;background-color:#666;"><!--上 右 下 左-->
	<img src="http://colah.github.io/posts/2015-08-Understanding-LSTMs/img/RNN-rolled.png" />
</div>
<p align = "center">循环神经网络带环</p>


&emsp;&emsp;在上图中，神经网络中的方框\\(A\\)输入\\(x_t\\),输出\\(h_t\\)。网络中的环总是让信息从某一时刻流向下一时刻。

&emsp;&emsp;这些环似乎成了循环神经网络的一个迷。然而，如果你多思考一下，它们并不比传统的神经网络复杂很多。一个循环神经网络可以看成是多个相同网络的复制，每一个节点把信息传给下一个。思考一下我们把环展开的时候发生了什么：

<div style="margin-left:20%;margin-right:20%;padding:20px 20px 20px 20px;background-color:#666;">
	<img src="http://colah.github.io/posts/2015-08-Understanding-LSTMs/img/RNN-unrolled.png"/>
</div>
<p align="center">展开的神经网络</p>

&emsp;&emsp;这个链状的结构揭示了循环神经网络与序列和列表密切相关的本质。它们是处理数据的神经网络的自然结构。

&emsp;&emsp;这无疑是有用的！在早些年，RNNs非常成功的应用到了这些方面：语音识别，语言建模，翻译，图像字幕……还有很多方面。我将就Andrej Karpathy优秀的博客[The Unreasonable Effectiveness of Recurrent Neural Networks](http://karpathy.github.io/2015/05/21/rnn-effectiveness/)讨论他用RNNs实现的惊人成果。这真的很有意义。

&emsp;&emsp;这些成功的本事是“LSTMs”的使用，“LSTMs”一种特殊的循环神经网络，对于很多问题，它比标准的神经网络表现得更好。几乎所有基于循环神经网络的激动人心的成果都是用它实现的。这也是写这篇文章的原因。

## 长时依赖问题
&emsp;&emsp;RNNs吸引人的原因之一是它能把先前的信息连接到当前的任务，例如把先前的视频帧的信息用于当前帧的理解。如果RNNs能做这些，它将是非常有用的。但是它能吗？并不能。

&emsp;&emsp;有些时候，我们仅仅需要寻找最近的信息去完成当前任务。例如，构建一个用先前信息预测下一个单词的模型。如果我们想要预测句子“The clouds are in the __*sky*__”中最后的词。我们不需要任何前面的语境——显然接下来的词就是***sky***。在这中情况下，所需要的相关信息和它的位置之间的间隔是很小的，RNNs能学会使用先前的信息。

<div style="margin-left:20%;margin-right:20%;padding:20px 20px 20px 20px;background-color:#666;"> 
	<img src="http://colah.github.io/posts/2015-08-Understanding-LSTMs/img/RNN-shorttermdepdencies.png"/>
</div>

&emsp;&emsp;但是有些场景也需要更多的上下文信息。思考预测文本“I grew up in French……I speek fluent *French*”中最后一个单词，最近的信息表明接下来的词可能是一种语言的名字，但是我们想要搞清楚是哪一种语言，我们需要很靠前的上文Franch。很可能相关信息和自身位置之间的间隔必须是很大的。
&emsp;&emsp;不幸的是，当这个间隔变得很大时，RNNs将很难学会联系相关信息。

<div style="margin-left:20%;margin-right:20%;padding:20px 20px 20px 20px;background-color:#666;"> 
	<img src="http://colah.github.io/posts/2015-08-Understanding-LSTMs/img/RNN-shorttermdepdencies.png"/>
</div>

---