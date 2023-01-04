+++
title = "Goal-oriented Coding"
author = ["Tze-Yang Tung"]
date = 2023-01-04
lastmod = 2023-01-04T11:50:03+00:00
tags = ["Projects"]
draft = false
weight = 2001
+++

## Project goal {#universal_coding_a}

This project aims to leverage [universal quantisation]({{< relref "information_theory#information_theory_d2" >}}) for goal-oriented coding.
It is closely related to the [rate-distortion-perception]({{< relref "information_theory#information_theory_b" >}}), as it was shown that under perfect perceptual constraint, a stochastic encoder/decoder can improve the distortion.


## Applications {#universal_coding_b}

A potential application for [universal quantisation]({{< relref "information_theory#information_theory_d2" >}}) in compression is in diffusion models.
Diffusion models essentially compute the VAE objective for every time step (usually using randomly chosen time step) to guide the decoder from isotropic noise to an image sample.
Although compression using diffusion models has been [demonstrated already](https://arxiv.org/abs/2209.06950), their approach relies on a latent variable with which the decoder is conditioned on, in order to guide decoder.
However, this is a tall ask for a single, one step, latent variable.
Typically, diffusion models will condition on all of the latents in each step, whereas here they are only conditioning on a single variable generated in one step.
This single variable is then quantised and coded as many prior compression works have used, e.g., uniform noise to approximate rounding error and a hyperprior density estimation.

A better approach would be to transmit those latent vectors in each step using universal quantisation.
Starting from the highest step, where the posterior is almost an isotropic Gaussian distribution, we send a sample from this distribution using universal quantisation and the decoder tries to approximate likelihood function.
\\( \forall t, x\_{t-1} \Rightarrow q(x\_t | x\_{t-1}) \stackrel{x\_t}{\Rightarrow} p(x\_{t-1} | x\_t) \Rightarrow \hat{x}\_{t-1} \\), compute VAE loss \\( l\_{ELBO}(x\_{t-1}, \hat{x}\_{t-1}) \\).
Moreover, the coding rate should be approximately constant since at each step \\( q(x\_t | x\_{t-1}) \approx p(x\_{t-1} | x\_t) \\), therefore the KL divergence should be small.
Essentially, the cost of coding is amortised into the diffusion steps.
Even though this doesn't make the coding speed any faster, it does provide a way to make rate-distortion trade-offs since we can stop coding after a certain point and obtain the intermediate image.

Another application is to consider a stochastic process which is conditional on a common random variable and the encoder wishes to communicate a sample of the stochastic process to a decoder.
This could be an interesting [goal-oriented communication problem]({{< relref "semantic_communications#semantic_communications_c" >}}).

Another interesting possibility is that because both the sender and the receiver are required to have a common random source, could the seed be used as a secret key for symmetric encryption?
Basically, without the common random source, is it possible to decode successfully, even if approximately?
(might be hard to prove the security of the scheme although maybe some kind of privacy guarantees can be made)

Another possibility is [**posterior matching**]({{< relref "information_theory#information_theory_e1" >}}).
Interestingly, there are some similarities between posterior matching and [universal quantisation]({{< relref "information_theory#information_theory_d2" >}}).
In posterior matching, the next transmitted random variable \\( X\_{n+1} \\) is independent of the previous received values \\( Y^n \\), to ensure that it represents new information.
Moreover, the message \\( \theta \\) can be almost surely uniquely recovered from \\( (X\_{n+1}, Y^n) \\) as \\( n \rightarrow \infty \\).
An important theorem that [universal quantisation]({{< relref "information_theory#information_theory_d2" >}}) relies on is the strong functional representation [(SFR)](http://dx.doi.org/10.1109/TIT.2018.2865570) lemma, which states that for general \\( (X, Y) \\), there exists a \\( Z \\) that is independent of \\( X \\) and \\( Y \\), and is a function of \\( (X, Z) \\), such that \\( H(Y | Z) \leq I(X; Y) + \log(I(X; Y) + 1) + 4 \\).
Therefore, if we map \\( Z \\) to \\( X\_{n+1} \\) in the previous problem and \\( Y \mapsto \theta \\), \\( X \mapsto Y^n \\), then we have \\( H(\theta | X\_{n+1}) \leq I(Y^n; \theta) + \log(I(Y^n; \theta) + 1) + 4 \\).
We can interpret \\( H(\theta | X\_{n+1}) \\) as any further uncertainty about the message after transmitting \\( X\_{n+1} \\) and \\( I(Y^n; \theta) \\) as knowledge the receiver has gained already.

We can apply [universal quantisation]({{< relref "information_theory#information_theory_d2" >}}) to the transmission of a stochastic policy to a remote agent for [MDPs]({{< relref "reinforcement_learning#reinforcement_learning_a" >}}) or [POMDPs]({{< relref "reinforcement_learning#reinforcement_learning_b" >}}).
That is, consider a controller which has policy \\( \pi(a | s) \\), and wishes to transmit a random action \\( a \sim \pi(a | s) \\) to a remote agent to carry out.
It is trivial to see that this corresponds exactly to the SFR lemma, where \\( Y \mapsto a \\) and \\( X \mapsto s \\).
It corresponds to the "sending actions" policy we have considered in the past.
A few scenarios to consider:

-   If we have an exact channel model \\( P(\hat{a} | a) \\), is there a JSCC equivalent of this?
-   What if we have noiseless feedback of \\( \hat{a} \\)?
-   What if the controller also needs to learn the optimal policy?
    In this case, it is possible that initially, we would communicate very little so that the remote agent simply takes random actions based on a prior distribution.
    As the controller learns based on the reward feedback of the agent's actions, it then begins to communicate what it thinks is optimal. This can be done using the common randomness (prior).
    The agent can construct an empirical distribution of the policy over time and once it has a sufficiently good estimate, no more communication is required.
    In this [paper](http://dx.doi.org/10.1109/ISIT50566.2022.9834399), a similar problem considering contexual multi-armed bandits (CMAB) was solved.
-   If we have a MA-POMDP, the problem can be seen as matching the joint distribution of the agents' actions with the optimal policy over all the agents.
    [Common randomness]({{< relref "information_theory#information_theory_b1" >}}) could also be useful in this case to reduce the communication load and could perhaps even be done with simply a [correlated random variable](http://dx.doi.org/10.1109/GLOBECOM42002.2020.9322460).
-   What if the action space is **continuous**? Universal quantisation could be used to quantise this to a finite rate? Can this be applied to a **goal-oriented quantisation** problem?
    This might be used in a sensor network.


## MA-POMDP Problem {#universal_coding_c}

Consider a 2-agent [POMDP]({{< relref "reinforcement_learning#reinforcement_learning_b" >}}), where the environment is governed by the transition kernel \\( P(s\_1^t, s\_2^t | a\_1^{t-1}, a\_2^{t-1}) \\) and the agents have policies \\( \pi\_i^t(a\_i^t | s\_i^t, m\_i^t)\\; i \in \\{0, 1\\} \\).
Let's assume that the union of the observations is in the state \\( s\_1 \cup s\_2 \in \mathcal{S} \\).
The messages \\( m\_i^t \\) are generated by the other agent and sent to the opposite agent.
We want to determine the minimum rate of the messages such that the agents converge to the optimal policy \\( \pi^\ast(a\_1^t, a\_2^t | s\_1^t, s\_2^t) \\).


### Initial thoughts {#universal_coding_c1}

It is valid to first consider what are the missing information for each agent to obtain stationarity.
For each agent, they need to be able to know the policy and observation of the other agent in order to have the complete information necessary to observe a stationary POMDP.
From the perspective of agent 1, if we know the transition kernel \\( P(s\_1^t, s\_2^t | a\_1^{t-1}, a\_2^{t-1}) \\), then if we know the action taken by agent 2, we can code the observation of agent 2 using \\( P(s\_2^t | a\_1^{t-1}, a\_2^{t-1}) \\) by marginalising over \\( s\_1^t \\).
In order to send the action, if we are given common randomness, then we can initialise both policies using the same random variable so that at \\( t = 1 \\), each agent simply codes the action taken based on \\( \pi\_i^1(a\_i^1) \\).
For \\( t > 1 \\), instead of coding the action based on \\( \pi\_i^t(a\_i^t | s\_i^t) \\), we can use relative entropy coding to send a sample \\( a\_i^t \sim \pi\_i^t(a\_i^t | s\_i^t) \\) using \\( 2^\epsilon \\) samples and \\( \epsilon \\) bits, where \\( \epsilon = D\_{KL}(\pi\_i^t || \pi\_i^{t-1}) \\).
Moreover, if we use a update policy like trust region policy optimisation (TRPO), then it is guaranteed that \\( D\_{KL}(\pi\_i^t || \pi\_i^{t-1}) \leq \epsilon \\).
Knowing the action and the observation of the other agent, the agent can update its belief of the other agent's policy exactly the same as the other agent.
Since the belief matches the policy exactly, \\( \pi\_i^t \\) becomes the new common randomness.
Therefore, in each round, the agents only need to send the amount of update that their policies changed by.
In fact, if we use something similar to epsilon greedy for exploration, then the prior chosen at the beginning would represent the exploration distribution and while epsilon is large at the beginning, fewer communication would be needed, and as epsilon gets smaller we would communicate more.

The thing that needs to be checked is whether or not no common randomness can achieve the same thing.
If no common prior on the policy is given at \\( t = 1 \\), then each agent can only form a belief that may not be the same as the policy of the other agent (private randomness).
Therefore, to send the action, the agent must use at least \\( H(a\_i^t | s\_i^t) \\) bits, based on the current policy \\( \pi\_i^t \\).
This can be significantly bigger than \\( \epsilon \\).
The difference would be even greater if the action space was continuous since the private randomness version would need to quantize, leading to loss in performance.

This project also merges well with deep RL since the agent policies can be parameterized by DNNs.
