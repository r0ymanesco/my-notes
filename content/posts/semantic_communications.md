+++
title = "Semantic Communications"
author = ["Tze-Yang Tung"]
date = 2023-01-04
lastmod = 2023-01-04T11:21:22+00:00
tags = ["ReadingList"]
draft = false
weight = 2001
+++

## Rate distortion interpretation {#semantic_communications_a}

Rate-distortion theory is described in detail [here]({{< relref "information_theory#information_theory_a" >}}).
An example of this can be seen in the project [DeepJSCC Stream]({{< relref "deepjscc_stream" >}}).
Specifically, [the approach]({{< relref "deepjscc_stream#deepjscc_stream_c" >}}) where a conditional transformer is used to predict the number of channel uses needed to achieve a certain reconstruction quality is exactly a rate-distortion problem.


## Relationship to text-to-image generators {#semantic_communications_b}

There seems to be a connection between semantics and text-to-image generators.
Clearly the semantic of the text and image are being matched to generate images that match the description of the text.


## Goal-oriented communications {#semantic_communications_c}

Goal oriented communication can best be described as a [remote rate distortion problem](http://dx.doi.org/10.1109/SSP.2005.1628773).
The idea is that a feature of the source is observed through only the source and the receiver's job is to reconstruct the feature, rather than the source.
In this case, the goal is the feature.
