+++
title = "WAIveform"
author = ["Tze-Yang Tung"]
date = 2023-01-03
lastmod = 2023-03-07T16:53:16-05:00
tags = ["Startup"]
draft = false
weight = 2001
+++

## Potential customer contacts {#waiveform_a}


### Qualcomm {#waiveform_a1}

-   Arash Behboodi (ai research)
-   Dashan Gao (machine learning, ai)
-   Tharak Krishnan (product and engineer leader)
-   Yasutomo Matsuba (principle engineer video)


### Apple {#waiveform_a2}

-   Said Medjkouh (RnD, wireless tech/ecosystem)
-   Alan Carlton (wireless research)
-   Kenza Hamidouche (wireless systems architect)
-   Krisztian Kiss (senior wireless standards)
-   Onur Sahin (wireless research)


### FB {#waiveform_a3}

-   Joel Kyungho Kim (head of cellular tech)
-   Jean-Yves Bouguet (head of ar/vr)


### BT {#waiveform_a4}

-   Ed Hunter (Cellular for Drones &amp; XCelerate Future Flight Project Manager)


### Intel {#waiveform_a5}

-   Gila Kamhi (chief ai officer)
-   Oner Orhan (AI/ML Research Scientist at Intel Labs)
-   Jodie Frew (IoT, ai, business acceleration)


### Imagination {#waiveform_a6}

-   Tim Atherton (director of ai)


### Arm {#waiveform_a7}

-   Sofiane Yous (Senior Principal Embedded AI/ML)


## Funding tips {#waiveform_b}


### Alexandria (Creator Fund) {#waiveform_b1}

There are basically 3 stages between idea to series A:

-   Pre-seed
-   Seed
-   Series A

In each round, expect to lose 15-25% of equity (25% is very bad).
By the time you reach series A, you need to not only be making revenue (not necessarily profit), but also hold 50% of the company between the people that work at the company full time.

There are tax benefits in the UK for individuals that invest in startups ([SEIS](https://www.gov.uk/guidance/venture-capital-schemes-apply-to-use-the-seed-enterprise-investment-scheme), [EIS](https://www.gov.uk/guidance/venture-capital-schemes-apply-for-the-enterprise-investment-scheme#how-the-scheme-works)), which is why angel investors will do this rather than invest in other things (e.g., stocks).
In other to be elegible for those UK tax benefits, you need to be an incorporated company.

A good way to calculate the ownership ratio is to consider how many hours each founder spends on the company each week.


### Tomas {#waiveform_b2}

For pre-seed, we'll want to establish a realistic problem and solution.
We don't need to generate any revenue, but the problem we want to solve should have both a financial and **emotional** pull.
For example, making a smarthome work better doesn't have a lot of emotional pull and might be harder to get funding compared to autonomous vehicle safety which is clearly a problem and lots of people will have known someone in a car accident.
The market size and potential financial return for AVs is also much larger than smarthome devices.
Even though AV space is much harder to enter, the potential for the market and our solution could lead to enough funding for entry.


## Smart home {#waiveform_c}


### Challenges {#waiveform_c1}


#### Security {#projects_c1a}

This is the same as Ana-Maria/Hamed's startup.
This is assumed to be handled in the application layer.


#### Connectivity/reliability {#waiveform_c1b}

This is where we might be able to innovate.
Typical smart home protocol uses 802.14.5 as the PHY standard.
It doesn't specify any channel codes, only a DSSS is used.
If we can come up with a very simple channel code that can run in software,
then this might improve the reliability of smart home devices.
Any solution needs to be very easy to implement (no new hardware requirements).

From looking at the Thread API, it seems that the main challenges worth addressing are:

-   Low data rate, short range

This is currently solved using a mesh network, which is a **multi-hop** problem.
An implementation of channel coding could also help but would have to be extremely efficient. Recall the key to this space is energy efficiency.
JSCC could certain help if we can treat the whole mesh as a single system and do some kind of task oriented coding.

-   Topological reconfiguration after node dropout

Thread implements self-healing, which is when a single device drops out of the mesh, it can reconfigure its topology without it.
It is not clear yet whether this reconfiguration takes into account the network environment.
If it doesn't, this can have a big impact on performance if the topology can be reconfigured to ensure reliability/latency due to channel quality.

-   Bandwidth management

This is due to the large number of devices that could be on the network.
It is not clear how they are managing this either, possibly not a lot of optimization into this.
This problem can be folded into the data rate/range problem.

In fact, these three problems can all be folded into one optimization problem.
[OpenThread](https://openthread.io/guides/thread-primer/) also offers open source APIs to test new algorithms.


#### Market segmentation/interoperability {#waiveform_c1c}

Is it possible for someone to come up with a universal API for all smart home standards?
I think this will be increasingly irrelevant as more manufacturers start using Thread.


### Multiple-access relay channel (MARC) {#waiveform_c2}

A smarthome network is basically a mutiple-access relay channel (MARC).


### Integrated sensing and communications (ISAC) {#waiveform_c3}

ISAC for multiple access channels (MAC) is also amiable to JSCC.
This is mostly due to the fact that MACs can gain more significantly than point-to-point.


### Customer discovery {#waiveform_c4}


#### Skydio {#waiveform_c4a}

The meeting verified our current solution cannot go to market but a discrete outter JSCC code using existing PHY implementation could.
It also verified that targetting markets that have solutions that are very good already will not succeed.
The issue is in order to just close the gap in hardware compared to existing implementations, the capital cost is already too high.
We need to target problems that don't have good solutions where the gains can be obtained without closing those gaps.


## Autonomous vehicles {#waiveform_d}


### Safety {#waiveform_d1}

Can we develop a inter-autonomous vehicle protocol so that we can improve the safety of AVs?
The motivation is as follows:

-   AVs are good for many general cases but fail at one in a million scenarios.
    With the help with other AVs, we can make a problem that happens in 1e-6 to 1e-9 for example.
-   Moreover, the models deployed in each car might be easier to train since they no longer have to be reliable on their own, but rather, reliable in aggregate.

An interesting idea that Tomas brought up is rather than trying to license this technology, we can instead form a network of cars ourselves and sell **access** to this network instead.
Although it might seem difficult to build such network, [comma.ai](https://comma.ai/) has demonstrated that it's possible to start a company in this space if the technology is sufficiently good.
We could even recruit Uber drivers and ask them to install an app on their phone that could build this network.
The drivers could opt in to such schemes and get paid for sharing this data, we would get a cut of that money.


### Customer discovery {#waiveform_d2}

[DIMO](https://dimo.zone/) is a company that tries to something similar.
The idea is that car owners record statistics about their car using the app and the company provides a service to them, such as insurance and resale value.
They are also going to be opening up APIs to allow others to either:

1.  Sell products to their customers directly (e.g., insurance)
2.  Use their data to perform some other purpose
3.  Build new hardware using their backend

However, this is still more of a data aggregation service and all of the inference is done in a offline, non-real time sort of way.
When asked whether he sees any real-time uses of this data, the co-founder Andy Chatham did not respond directly.
He also mentioned that the major problems in the AV space is mostly infrastructure related and that he thinks only when these issues are resolved can you build more advanced experiences on top of that.
Those issues include:

-   How to meet the charging demand since almost all AVs today are electric?
-   How to store them and deploy them so that their up time is maximised?
    -   This is more about robotaxis, which he also thinks is the only viable market that will use AVs in the near future.

We have also spoken to Markus Hantschmann from [Roadia](https://www.roadia.com/en/home), a startup in Germany focusing on V2X for vehicle safety.
There, their biggest challenge is to convince officials that their technology is worth pursuing.
An important thing to keep in mind is **who is benefiting**?
Politicians have very different incentive structures to corporations, so it's important to keep that in mind when approaching them.
Traffic management centres are also great sources of information, if you can get a hold of them.
One thing that was quite interesting to hear is that, rather than deploying new hardware, cities actually already have lots of hardware installed, but no one to really aggregate that information.
For example, traffic cameras, sensors, temperature readings...etc.
A software solution utilising existing hardware would be much cheaper to show and officials would like that since it doesn't involve spending more public money.
It may also shorten the time to deployment since working with the government always involves a lengthy process to secure funding and permission...etc.
However, in general, this type of work is better done in Europe because the government is more centralised.
London for example has city labs, which is a testbed for V2X ideas.


## Generative models {#waiveform_e}


### Safety/Accuracy {#waiveform_e1}

Following the same line of thinking for AVs, monolithic LLMs have shown tremendous progress but it's also clear that they lack safety features that prevent them from outputting text that may be harmful.
That includes racist content as well as falsehoods that are presented confidently.
[Google](https://ai.googleblog.com/2022/01/lamda-towards-safe-grounded-and-high.html) has been thinking about this quite a bit clearly, since this is directly going to affect their most important product.
[DeepMind](https://www.deepmind.com/publications/characteristics-of-harmful-text-towards-rigorous-benchmarking-of-language-models) also submitted a benchmark paper to NeurIPS last year that tries to quantify harmful output of a LLM.
But I think this is still a huge open question that needs addressing ASAP and similar to the idea we have about AVs, perhaps the onous should not be on individual models but rather the output of LLMs should be fact-checked by querying multiple sources.
Similar to how humans research, LLMs should also check sources other than itself.
This may not completely rid LLMs from outputting incorrect things, as a human cannot always get everything correct, but it will surely improve the reliability.
From the perspective of machine-to-machine communication, think of this as LLMs talking to other computing platforms, whether that is Wikipedia or literally writing an email to a professor somewhere to ask for an opinion.
This might be the missing ingredient to making these models, which are increasingly affecting our daily lives, more trustworthy.

This idea is certainly not limited to LLMs, as diffusion models also have a similar issue, where it can be misappropriated to output images that are inappropriate or illegal.
Moreover, there is increasingly concerns that it violates the copyright of other people's work.
If it can check that someone has copyrighted something extremely similar to its output, then it might choose to output something else that is sufficiently different.


### Aggregation {#waiveform_e2}

There are increasingly generative AI tools that are open to access by regular users.
But not everyone knows how to use it or that they even exist.
Can we have a common interfact, similar to Google search where depending on what kind of result you want (text, images...), you can select the relevant "tab" and use a generative AI tool to get a result?
Moreover, can we create a single search box, which breaks down a search query into multiple queries and sends them to the relevant models?
This might even be achievable with prompt engineering, designing inputs that help us get the output we want.
The could create a much richer search experience, multi-modal and more informative.
It would certainly compliment the [safety angle](#waiveform_e1).
