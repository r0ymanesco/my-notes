+++
title = "WAIveform"
author = ["Tze-Yang Tung"]
date = 2023-01-03
lastmod = 2023-01-06T21:12:02+00:00
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


## Funding tips from Alexandra {#waiveform_b}

There are basically 3 stages between idea to series A:

-   Pre-seed
-   Seed
-   Series A

In each round, expect to lose 15-25% of equity (25% is very bad).
By the time you reach series A, you need to not only be making revenue (not necessarily profit), but also hold 50% of the company between the people that work at the company full time.

There are tax benefits in the UK for individuals that invest in startups ([SEIS](https://www.gov.uk/guidance/venture-capital-schemes-apply-to-use-the-seed-enterprise-investment-scheme), [EIS](https://www.gov.uk/guidance/venture-capital-schemes-apply-for-the-enterprise-investment-scheme#how-the-scheme-works)), which is why angel investors will do this rather than invest in other things (e.g., stocks).
In other to be elegible for those UK tax benefits, you need to be an incorporated company.

A good way to calculate the ownership ratio is to consider how many hours each founder spends on the company each week.


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

-   Low data rate, short rage

This is currently solved using a mesh network, which is a **multi-hop** problem.
An implementation of channel coding could also help but would have to be extremely efficient. Recall the key to this space is energy efficiency.
JSCC could certain help if we can treat the whole mesh as a single systme and do some kind of task oriented coding.

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


## Customer discovery {#waiveform_d}


### Skydio {#waiveform_d1}

The meeting verified our current solution cannot go to market but a discrete outter JSCC code using existing PHY implementation could.
It also verified that targetting markets that have solutions that are very good already will not succeed.
The issue is in order to just close the gap in hardware compared to existing implementations, the capital cost is already too high.
We need to target problems that don't have good solutions where the gains can be obtained without closing those gaps.
