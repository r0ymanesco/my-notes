+++
title = "Information Theory"
author = ["Tze-Yang Tung"]
date = 2023-01-03
lastmod = 2023-01-10T15:29:29+08:00
tags = ["ReadingList"]
draft = false
weight = 2001
+++

## Rate-distortion {#information_theory_a}

_This is fairly straight forward, will complete later._


### Universal coding {#information_theory_a1}

Universal coding is concerned with meeting a distortion constraint when the source distribution \\(p\\) is unknown.
A coding scheme is said to be _universal_ over a class of source distributions if the rate redundancy converges to zero for every source in that class.


### d-semifaithful coding {#information_theory_a2}

\\(d\\)-semifaithful coding is a form of lossy compression in which the decoder outputs a reconstruction sequence that is within distortion \\(d\\) of the original source sequence **with probability 1**.
A novel variation of [universal](#information_theory_a1) \\(d\\)-semifaithful coding is in which the distortion measure is revealed to the encoder alone and only when it receives the source sequence \\(x^n\\) to compress.
It is referred to as the _universal distortion_ problem in the [literature](https://dx.doi.org/10.1109/ISIT50566.2022.9834395).

A natural question to ask is if this class of coding problems can benefit from [common randomness](#information_theory_b1).


## Rate-distortion-perception {#information_theory_b}

-   **Definition (Rate-distortion-perception)**:
    For a Polish metric space \\(\mathcal{X}\\), let \\(( \mathcal{X}, \mathbb{B}( \mathcal{X} ) )\\) be the Borel measurable space induced by the metric.
    Let \\(\mathcal{P}( \mathcal{X} )\\) denote the set of distributions defined over \\(( \mathcal{X}, \mathbb{B}( \mathcal{X} ) )\\), and let \\(X\\) be a random variable with distribution \\(p\_X \in \mathcal{P}( \mathcal{X} )\\).
    Moreover, let \\(\Delta : \mathcal{X} \times \mathcal{X} \mapsto [0, \infty)\\) be a distortion function with \\(\Delta(x, \hat{x}) = 0 \leftrightarrow x = \hat{x}\\) and let \\(d : \mathcal{P}( \mathcal{X} ) \times \mathcal{P}( \mathcal{X} ) \mapsto [0, \infty]\\) be a divergence with \\(d( p\_X, p\_{\hat{X}} ) = 0 \leftrightarrow p\_X = p\_{\hat{X}}\\).
    The rate-distortion-perception function for \\(X\\) is given by
    \\[ R(D, P) = \inf\_{ p\_{ \hat{X}|X } } I(X; \hat{X}) \\]
    subject to \\(\mathbb{E}[ \Delta(X, \hat{X}) ] \leq D\\), \\(d(p\_X, p\_{ \hat{X} }) \leq P\\).


### Common randomness {#information_theory_b1}

In [this paper](https://arxiv.org/abs/2202.04147), common randomness is useful to reduce the rate of communication required to achieve a certain distortion-perception point even when the randomness is independent of the source.
Consider a random source with rate \\(R\_0\\) and a code rate of \\(R\\). In essence, there are \\(2^{nR\_0}\\) codebooks, each with \\(2^{nR}\\) codes.
The common random codeword selects the codebook to use and it can be made to match the distribution of the source.
This is interesting and might be useful in [MAC](#information_theory_c) and [smarthomes]({{< relref "waiveform#waiveform_c2" >}}).

However, the benefits of common randomness only seems to appear in the **perfect realism** regime (i.e. \\(d(p\_X, p\_{ \hat{X} }) = 0\\)), as this [this paper](http://dx.doi.org/10.1109/JSAIT.2022.3231820) shows.
Moreover, it is only benefitial when the definition of perfect realism is one where \\(p\_X = p\_{\hat{X}^n}\\).
That is, the density divergence is measured jointly rather than single letter.
If measured only by additive single letter distortion, then **private randomness** (i.e., the encoder and decoder both have independent sources of randomness) is sufficient to achieve the optimal rate.


## Multiple-access channel (MAC) {#information_theory_c}

_TBD_


### Multiple-access relay channel (MARC) {#information_theory_c1}

_TBD_


## Quantisation {#information_theory_d}


### Approximate methods {#information_theory_d1}

Most works consider rounding and using uniform noise to approximate the rounding noise.
This has been a massively successful approach; pretty much every paper on compression uses this method.
However, this leads to a mismatch between training and test time performance.

In terms of novelty, this [paper](https://proceedings.mlr.press/v119/yang20a.html) uses the posterior to encode the sample into bits, inspired by arithmetic coding.


### Universal quantisation {#information_theory_d2}

In this [paper](https://proceedings.neurips.cc/paper/2020/file/92049debbe566ca5782a3045cf300a3c-Paper.pdf), a _universal quantisation_ scheme is proposed where the problem of communicating a sample \\(z\\) from a noisy channel \\(p(z|x)\\) using as few bits as possible is considered.
Theoretically, it is shown that the number of bits to communicate the sample \\(z\\) is not much larger than the mutual information \\(I(x; z)\\) (see [paper](https://proceedings.neurips.cc/paper/2020/file/92049debbe566ca5782a3045cf300a3c-Paper.pdf) citation).

This quantisation technique is practically implemented in this [paper](https://arxiv.org/abs/2010.01185), called relative entropy coding (REC), where an image compression problem is considered using this technique.
Relative entropy coding can be defined as follows

-   **Definition (Relative entropy coding)**:
    Let Q be a target and P be a proposal distribution, with \\(D\_{KL}(Q || P) < \infty\\), and let \\(S = (s\_1,s\_2, ...)\\) be an infinite sequence of publicly available independent fair coin tosses.
    Relative entropy coding is the problem of producing a uniquely decodable code \\(C\\) representing a sample from \\(Q\\) given \\(S\\), such that the codelength \\(|C|\\)  satisfies \\( E[|C|] = \mathcal{O}(D\_{KL}(Q || P)) \\).

The key observation is that, in the VAE setup used in that paper, there is a common random distribution, which is the prior of the VAE \\( p(z) \\).
Therefore, the amount of information that needs to be sent is only characterised by the KL divergence between the posterior and the prior \\( D(p(z|x) || p(z)) \\).
This is highly related to [rate-distortion-perception](#information_theory_b) as shown in this [paper](https://arxiv.org/abs/2102.09270).
The authors show that if it is required that perception (distribution) is perfectly matched, then in some settings, stochastic encoders/decoders with [common randomness](#information_theory_b1) (e.g., universal quantisation), can achieve lower **distortion** than deterministic ones.

An interesting potential use case for universal quantisation is **channel feedback**.
In a wireless communication channel, the posterior \\( p(z|x) \\) is determined by the channel.
Then in order to losslessly send a sample \\( z \sim p(z|x) \\) from the receiver to the transmitter,
REC stipulates that at least \\( \mathcal{O}(D\_{KL}(Q || P)) \\) many bits need to be used.
However, the problem is that the receiver only ever observes one sample from the posterior but the universal quantisation method samples from a prior rather than from the posterior.
Instead, we can use the hybrid coding scheme, introduced in this [paper](https://proceedings.mlr.press/v162/theis22a.html), where instead of only sending the index, both the **quantised** sample and the index of the candidate are transmitted.
The coding cost of the index is the same while there is an additional cost for transmitting the quantised sample.
It is unclear whether this can give good results compared to conventional paradigm of quantising directly.

A major problem of REC is that without any assumptions on \\(Q\\) and \\(P\\), the runtime of any REC algorithm is \\( \mathcal{O}( \exp( D\_{\infty}(Q||P) ) ) \\).
However, if \\( dQ/dP \\) (the density ratio) is unimodal, then it can be done in \\( \mathcal{O}( D\_{\infty}(Q||P) ) \\) (see [here](https://arxiv.org/abs/2010.01185)) .

The key thing to note about REC is that because the indices have a distribution equal to the posterior, sampling the index is equivalent to sampling from the posterior distribution.
Therefore, a code which uniquely identifies the index of a sample can recover the sample exactly.
This is a little mind bending. It is somewhat counter intuitive that an integer can have a distribution which is the same as a continuous random variable.
But I suppose the key is that the common random randomness is itself continuous, which affords the code to be discrete.
Of course, there is also the bias, which never goes away but is negligible if the number of samples from the prior is large enough.
The cost is that, for each sample transmitted, a large number of candidates need to be drawn, which is where the \\( \mathcal{O}( \exp( D\_{\infty}(Q||P) ) ) \\) runtime complexity comes from.

An important theorem that universal quantisation relies on is the strong functional representation [(SFR)](http://dx.doi.org/10.1109/TIT.2018.2865570) lemma, which states that for general \\( (X, Y) \\), there exists a \\( Z \\) that is independent of \\( X \\) and \\( Y \\) is a function of \\( (X, Z) \\), such that \\( H(Y | Z) \leq I(X; Y) + \log(I(X; Y) + 1) + 4 \\).


## Feedback {#information_theory_e}

It is known that feedback does not increase the capacity of a point-to-point channel but can increase reliability.


### Posterior matching {#information_theory_e1}

[Posterior matching](http://dx.doi.org/10.1109/TIT.2011.2104992) is a generalised noiseless feedback scheme that is doubly exponentially capacity achieving for AWGN channels.

-   **Theorem (Optimal posterior matching)**:
    Let \\( \theta \\) be the message point, \\( P\_X \\) the channel input distribution, \\( P\_{Y|X} \\) the channel likelihood, and \\( g\_n \\) be the transmitter function at time step \\( n \\).
    Then the optimal posterior matching scheme is defined as \\( g\_1(\theta) = F^{-1}\_X (\theta) \\), \\( g\_{n+1}(\theta | y^n) = (F^{-1}\_X \circ F\_{X | Y}(\cdot | y\_n)) \circ g\_n(\theta | y^{n-1}) \\).

The way to think about posterior matching is that the transmitter, observing the noiseless feedback of the receiver's belief, is trying to send new information so that the receiver is steered to the correct answer.

Interestingly, there are some similarities between posterior matching and [universal quantisation](#information_theory_d2).
In posterior matching, the next transmitted random variable \\( X\_{n+1} \\) is independent of the previous received values \\( Y^n \\), to ensure that it represents new information.
Moreover, the message \\( \theta \\) can be almost surely uniquely recovered from \\( (X\_{n+1}, Y^n) \\) as \\( n \rightarrow \infty \\).
Therefore, considering the [SFR](http://dx.doi.org/10.1109/TIT.2018.2865570) lemma, where it is shown that for general \\( (X, Y) \\), there exists a \\( Z \\) that is independent of \\( X \\), and \\( Y \\) is a function of \\( (X, Z) \\), such that \\( H(Y | Z) \leq I(X; Y) + \log(I(X; Y) + 1) + 4 \\).
If we map \\( Z \\) to \\( X\_{n+1} \\) in the previous problem and \\( Y \mapsto \theta \\), \\( X \mapsto Y^n \\), then we have \\( H(\theta | X\_{n+1}) \leq I(Y^n; \theta) + \log(I(Y^n; \theta) + 1) + 4 \\).
We can interpret \\( H(\theta | X\_{n+1}) \\) as any further uncertainty about the message after transmitting \\( X\_{n+1} \\) and \\( I(Y^n; \theta) \\) as knowledge the receiver has gained already.
