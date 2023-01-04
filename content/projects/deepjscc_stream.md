+++
title = "DeepJSCC Stream"
author = ["Tze-Yang Tung"]
date = 2023-01-04
lastmod = 2023-01-04T10:29:08+00:00
tags = ["Projects"]
draft = false
weight = 2001
+++

## Using transformers to autoregressively predict the next codeword {#deepjscc_stream_a}


### Poor results {#deepjscc_stream_a1}

The encoding/decoding approach used so far has not worked:

-   The implementation that uses 2D transformers eventually diverged and crashed.
-   The impelmentation using [VTC transformers](https://arxiv.org/abs/2206.07307) stopped improving and produced bad results, weaker than the 2D transformers model.

However, the VCT approach seems to be more promising as the transformer video compression paper (VCT, Mentzer) has shown it can work.
It is also much faster to train.
It would make more sense to try to improve the current implementation.


### New encode/decode scheme {#deepjscc_stream_a2}

What transformers are really good at is autoregression.
So the question we want to pose to a transformer is: "given the prior tokens, what is the next token?"

Given frames \\( \\{x\_1, ..., x\_n\\} \\), with corresponding features \\( y\_i = E(x\_i) \\).

-   Encoder: learn a "query" token \\( Q\_z \\) that learns the codeword given a sequence of frame features \\( \\{y\_1,...,y\_n\\} \\).
    That is, we are modelling \\( P(z\_n | y\_1, ..., y\_n) \\) by querying the self-attention output of \\( \\{y\_1,...,y\_n\\} \\).
    This can be done sequentially as frames arrive and we can observe the entropy of \\( z\_n \\) to determine the GoP cut-off.
    **Causality is naturally preserved**.
    **Note that this is NOT an autoregressive process**.
    If we want the codeword to be autoregressive, we can condition on \\( (y\_i, z\_{n-1}) \\).
-   Decoder: given \\( z\_n \\), learn a "start decode" token \\( \hat{y}\_0 \\), such that we autoregressively decode the frame features.
    That is, we are modelling \\( P(\hat{y}\_n | \hat{y}\_0,..., \hat{y}\_{n-1}; z\_n) \\).
    The estimated frame can then be obtained as \\( \hat{x}\_n = D(\hat{y}\_n) \\).
    **To ensure causality, the decoding should be masked**.

Unfortunately the new design still does not work.
The next step is to try an autoregressive encoder.
The encoding will be slower due to the iterative nature of the code but it may be better due to the progressiveness rather than single step coding.

Another option is to implement the same idea but using 2D transformers.
This is easier so will go first.


### Connection to R-D {#deepjscc_stream_a3}

Given a channel bandwidth constraint, you could in theory traverse the R-D trade-off by considering,
given a target distortion, how many frames can I encode into the available channel resources?
The distortion would be lower-bounded by the distortion offered by encoding 1 frame.
More frames encoded implies lower rate (per frame) and therefore higher distortion.
This is in contrast to typical R-D trade-off, as this implies that greater latency (encoding more frames) increases distortion.


## Snapshot compressive imaging (SCI) {#deepjscc_stream_b}

The [STformer](http://dx.doi.org/10.1109/TPAMI.2022.3225382) seems to be a good way to perform 2D transformer.
Moreover, the [snapshot compressive imaging (SCI)](https://arxiv.org/pdf/2103.04421.pdf) problem, sounds very interesting and may be applicable to our problem.
In the SCI formulation, the encoding is done via the summation of frames, each multiplied with a mask.
Then given the mask, and the result of the summation, the decoder tries to invert the process.
This is akin to an under-determined inverse problem.
If we use this for our coding problem, this would essentially entail a simple encoder, where each frame is mapped to a feature and those features are summed together to form the codeword.
Then the decoder, knowing the filters used in the encoding process, inverts the summation and reproduces the original frames.

SCI is basically an inverse problem (very similar to compressive sensing).
We can use a transformer to learn the masks for each feature tensor and sum them up.
The receiver then computes the inverse problem given the "measurements" (masks).
If we want to achieve autoregressive coding, we can just **condition the next mask on the previous masks**.
The decoder can be like the [STformer](http://dx.doi.org/10.1109/TPAMI.2022.3225382)

This idea has been shelved for now as it requires the masks to be sent separately.


## VCT based R-D prediction {#deepjscc_stream_c}

We can use the same model as VCT to predict the distribution and use it to determine the number of channel uses.
Then a second transformer would predict the image/video quality instead of the distribution.
Once both transformers are trained, we can traverse the R-D curve freely.
That is, we first map frames into the latent space via an encoder \\( z\_t = E(x\_t) \\).
Then, we want to estimate \\( Q(z\_t) = f\_\theta(z\_{t-1},...,z\_0) \\), where \\( Q \\) is the quality function, parameterised by \\( \theta \\).

To generate codewords, we condition the current frame's latent vector on the previous frames' latent vectors \\( c\_t = f(z\_t | z\_{t-1},...,z\_0) \\), using a transformer.

Currently, the coding stage uses reconstructions from the receiver at a random rate to infer the current frame.
This might lead to suboptimal results as initial reconstructions are poor, therefore affecting the reconstructions of future frames.
However, accumulating gradients across the entire video sequence would require too much memory.
Perhaps there is a way to do curriculum training, starting with feature encoder only before jointly training with the transformer, to find a middle ground.

In order to preserve the ordering of rate-distortion curve, the output of the predictor is a cumulative sum.
The predictor training using a transformer conditioned on prior frames does indeed work.
The question is whether the accuracy is good enough to determine the rate.
This result is also based on codewords which are the output of the feature encoder, without prior frame conditioning.


### Compression quality prediction {#deepjscc_stream_c1}

The same concept can be used to predict the compression quality for a given number of bits.
The VCT paper only considers average quality for a given number of bits.
If we allow variable rate compression, then we can instead target a fixed quality and adjust the bit-rate to obtain it.
The result would be **for a chosen distortion, an average bit-rate to achieve it**.


### Architecture {#deepjscc_stream_c2}

According to the [VCT video compression](https://arxiv.org/abs/2206.07307) paper, it is possible to train jointly with this architecture using the architecture in [ELIC](http://dx.doi.org/10.1109/CVPR52688.2022.00563).
Preliminary results seems to suggest a pretty substantial improvement using this architecture, over the one proposed by VCT.
The primary difference is that the GDN layers are replaced by residual layers.
It is interesting to see that the GDN layer is becoming out of fashion.

On the other hand, the latent residual predictor proposed by the VCT paper was not useful for our case since the codeword and the decoder both already condition on the previous frames as well as the current frame tokens.


### Hyperparameters {#deepjscc_stream_c3}

In the [ELIC](http://dx.doi.org/10.1109/CVPR52688.2022.00563) paper, they suggest using dynamic weights on the loss of different rates to improve the generalisation for each rate.
There are two approaches:

-   The idea is that, let \\( D\_r \\) be the average distortion of the model at rate \\( r \\), then if the average improves by more than a constant factor \\( \delta \\), then the weighting \\( \mu\_r \\) at that rate will decrease, and vice versa.
    The loss is then \\( \sum\_r \mu\_r D\_r \\).
-   Another approach is to compare frames at successive rates and ensure that the improvement gap between each successive rate is sufficiently large.

The paper actually suggests using a different rate for each delay (time instance from the last key frame), but right now we are just averaging over time and using a single weight for each rate.
Although the results suggest the first approach works better, it seems that the weighting needs to be normalised as the gradients become unstable if the weights are too large.
In the first method, the gradients became unstable after \\( \mu\_r > 8 \\) but could be much lower in theory as the second method became unstable after \\( \mu\_r > 4 \\).
This is not surprising as the weighting scales the gradient.


### Ablation study {#deepjscc_stream_c4}

In terms of how codewords are generated, there are two methods:

-   We can simply take the latent vector of each frame \\( c\_t = z\_t \\), and use the predictor \\( Q \\) to determine how many tokens to transmit.
-   We can obtain the codeword conditioned on the previous frames \\( c\_t = f(z\_t | z\_{t-1},...,z\_0) \\).

The results of this study showed that the conditional codewords perform better, likely because the encoder can learn to discard unnecessary information if it knows what has already been transmitted.

In terms of the predictor:

-   Take the conditional codewords and use a MLP to predict the quality of each rate.
-   Use the input of the conditional codeword transformer and use a separate transformer to predict the quality at each rate.
-   Use the output of the conditional codeword transformer and run a second transformer on those codewords **conditioned** on the prior codewords.

Both the second and third methods work well. To reduce the memory usage during training, the second method is preferred.

In terms of references for decoding, the following two approaches are considered:

-   Using the decoded frames to train a separate auto-feature encoder
-   Use the noisy codewords from the prior transmission directly

Likewise, for encoding, the encoder can either use zeros to initialize the prior frame latent conditioning, or use learned features obtained from mapping all zero frames to the latent.
The second approach uses less memory and is equally good.


### Bandwidth allocation {#deepjscc_stream_c5}

Having obtained a predictor/entropy model of the tokens, we would now like to allocate bandwidth according to this information.
Chenghong has a method where we input the bandwidth ratio into a MLP along with the tokens and that allows the MLP to learn to allocate bandwidth accordingly.
However, we would like the bandwidth input to also be a learn-able parameter.
The point is to avoid ordered information, as currently the predictor/entropy model tells us how many tokens are needed to achieve a certain quality if the tokens were transmitted sequentially.
This has proven to be a performance degrader, compared to unordered encoding.
