---
title: "Redis's Founder Quit" 
date: 2020-07-02
tag: Redis Open-Source Software
---

## Redis's Founder

其实是昨天早晨看到这个贴子的，觉得有点意思，于是看了一下原文和作者在1年前写的一篇关于开源软件的帖子，感受还是挺怪的，可以记录一下

这个帖子的标题其实也是蛮有意思的，作者认为自己开发Redis的过程就是一段冒险，现在就是这个冒险的过程的终结了

[The end of the Redis adventure](http://antirez.com/news/133)

antirez是一个意大利人，这个名字不清楚是什么意思，wiki上说这是它的Nick Name。那么我们还是看一下作者都说了什么吧。

当然博文的一开始，肯定还是回忆过去了，因为回忆过去总是比较好的，毕竟是good old times:

When I started the Redis project more than ten years ago I was in one of the most exciting moments of my career. My co-founder and I had successfully launched two of the major web 2.0 services of the Italian web. In order to make them scalable we had to invent many new concepts, that were already known in the field most of the times, but we didn’t know, nor we cared to check. Problem? Let’s figure out a solution. We wanted to solve problems but we wanted, even more, to have fun. This was the playful environment where Redis was born.

当我开始Redis这个项目的时候，其实已经是十多年前了，那个时候从现在回忆来看，依然是我的生涯中最令人激动的时刻。我们为了解决一些web站点的可扩展性的问题，我们想出来一个解决方案，也就是我们今天看到的Redis，我们像解决问题，但是我们更多想要的东西，是fun，乐趣很重要。这就是Redis诞生的环境。

But now Redis is, incredibly, one of the main parts of so many things. And year after year my work changed from building this thing to making sure that it was also as useful as possible, as reliable as possible. And in recent years, what I do every day changed so much that most of my attention is spent in checking what other developers tell me about the Redis code, how to improve it, the changes it requires to be more correct or faster or more secure. However I never wanted to be a software maintainer.

但是现在来看，Redis无可争议的，已经成为了非常多的东西的计出构件。然后一年一年的过去，我的工作已经从纯粹的创造，变成了另外一种工作，就是尽量让Redis更加有用，更加稳定。特别是在最近的几年来看，我的注意力基本上都花费在阅读其他开发人员对于Redis代码的意见，如何改进代码，如何让Redis变得更加快，更加安全。然而，这些并不是我喜欢的，我从未想过成为一个软件的维护人员。

I write code in order to express myself, and I consider what I code an artifact, rather than just something useful to get things done. I would say that what I write is useful just as a side effect, but my first goal is to make something that is, in some way, beautiful. In essence, I would rather be remembered as a bad artist than a good programmer. Now I’m asked more and more, by the circumstances created by a project that became so important, to express myself less and to maintain the project more. And this is indeed exactly what Redis needs right now. But this is not what I want to do, and I stretched myself enough during the past years.

作者说，我写代码的目标是为了表达自己，我更认为我写的代码是艺术品，而不是一些对其他项目有用的东西。我更愿意说我写的东西有用完全是出于一种副作用，我的第一目标是创造一些我认为非常美的东西。我想表达的核心的意思是，我更愿意你们认为我是一个艺术家，甚至是不太好的艺术家都可以，而不是被人们认为是一个好的程序员。

现在我创造的项目变得越来越重要了，以至于我不能表达自己，而被迫选择更多的来维护一个软件。让Redis更加的稳定是Redis需要的，但是这并不是我需要的。我在过去的数年中，已经被挤压的足够多了。

其实帖子的后面还有足够多的内容，积极的正面的引导，对于未来的鼓励，对于社区的宽慰，但是帖子真正表达的东西，在前三段都已经将明白了。

作者喜欢纯粹的创造的过程，而不是后续不断的维护的过程。

## The struggles of an open source maintainer

作为一个开源软件维护者的挣扎

这个帖子恰好是作者在quit这个帖子中提到的事情，因为越来越多的时间被迫投入到软件的维护工作中，作者越来越少的编写自己喜欢的博客，也不能写文章了。所以我们从作者的blog往前看，那么倒数第二篇有表达自己的帖子就是这一篇帖子了，从标题来看这是作为一个软件维护人员的挣扎。

而且是作者答复另外一个OSS Maintainer的帖子，看来作为维护的人员，心路都是很挣扎的。其实从这篇帖子中就能看出，为什么作者会退出。因为确实有非常多痛苦的事情。

### Flood effect

I don’t believe in acting fast, thinking fast, winning the competition on time and stuff like that. I don’t like the world of constant lack of focus we live in, because of social networks, chats, emails, and a schedule full of activities. So when I used to receive an email about Redis back in the early times of the project, when I still had plenty of time, I was able to focus on what the author of the message was trying to tell me. Then I could recall the relevant part of Redis we were discussing, and finally reply with my real thoughts, after considering the matter with care. I believe this is how most people should work regardless of what their job is.

作者的第一个观点就是，我不相信，acting fast, thinking fast这类的东西，因为从作者来看这样根本不能产出什么有用的东西。

作者说我不喜欢现在的世界，因为各种各样的社交媒体等等，我们的注意力是非常分散的，我们的日程表被排的满满的。

所以回顾过去呢，当我之前收到一个关于Redis的邮件的时候，我会关注作者表达的本意，他想告诉我什么，然后我会认真的思考表达我的真实的想法，这些想法都是我认真思考的，充满了我对它的关心和思考。而这种态度也是我认同的，不管做任何工作都应该有的态度。

When a software project reaches the popularity Redis reached, and at the same time once the communications between individuals are made so simple by the new social tools, and by your attitude to be “there” for users, the amount of messages, issues, pull requests, suggestions the authors receive will grow exponentially. At the same time, at least in the case of Redis, but I believe this to be a common problem, the amount of very qualified people that can look at such inputs from the community grows very slowly. This creates an obvious congestion. Most people try to address it in the wrong way: using pragmatism. Let’s close the issue after two weeks of no original poster replies, after we ask some question. Close all the issues that are not very well specified. And other “inbox zero” solutions. The reality is that to process community feedbacks very well you have to take the time needed, otherwise you will just pretend your project has a small number of open issues. Having a lot of resources to hire core-level experts for each Redis subsystem, to work at OSS full time, would work but is not feasible.

当软件项目达到一个流行程度之后，同时个体之间的沟通方式也被现在社交工具大大简化，同时因为社交大大简化之后，你的态度也会变化，这些都会让消息，问题，PR，建议以一个指数的速度增长。

然而作为一个通常的情况来看，在Redis社区中足够Qualified的人，可以处理这些意见的人的数量，增加的很慢。

这就更加的显著的增加了拥塞，而且大多数人会倾向使用错误的方式来解决问题：实用主义/pragmatism

所谓的实用主义，作者也说了，粗暴的关闭一些2周没人回复的issue，关闭所有描述的不是很详尽的issues，以及其他各种各样的需求和问题清零的策略。

然后事实是处理社区的反馈，而且想要处理的好的话，你需要足够多的时间，否则你只能假装你的项目只有一小部分open issues。当然花很多钱来雇佣高级别的专家来负责Redis的各个子系统，为了OSS事业做全职工作，都是解决办法，但是太贵。

So what happens? That you start to prioritize more and more what to look at and what not. And you feel you are a piece of shit at ignoring so many things and people, and also the contributor believes you don’t care about what others have to give you. It’s a complex situation. Usually the end result is to develop an attitude to mostly address critical issues and disregard all the new stuff, since new stuff are yet not in the core, and who wants to have a larger code base with even more PRs and issues there? Maybe also written in a more convoluted way compared to your usual programming style, so, more complexity, and good luck when there is a critical bug there to track the root cause.

基于以上的理由，你开始把越来越多的东西进行优先级排序，你排序你认为重要的，和你认为不重要的，都是越来越多。然后你会感觉到你自己就是 a piece of shit，因为你忽略了这么多的事情和人，同理那些热情的贡献者认为你不关心他们的贡献。

情况复杂难以处理。通常来说，在最终结果会怎么样呢？通常来说我们会发展出一种态度，一种忽视一切的态度，尤其是忽视new stuff，因为你的attitude都被address到critical issus上了。毕竟新的stuff会带来新更大的代码仓，更多的PR和Issues，而这些恰好是我们不想要的。可能这些新的PR还是用了一种与你现有的代码仓完全不同，且更复杂的编码方式来实现的，所以这为你的项目带来了更多的复杂性，然后当你遇到critical issues的时候，你只能祈求一些good luck来锁定根因。

@todo