# Investigating Sample Inefficiency in Reinforcement Learning

***See .pdf for full report.***

# Introduction
> While reinforcement learning alludes to promises nearing Artificial General Intelligence, the purpose of this project is motivated by the very criticisms that refute the efficacy of RL [Irpan, 2018]
Such claims point to RL's sample inefficiency, complex reward function design, generalization
problem and arguments for other, more classical robotics techniques. We investigate the validity of these claims as well as architectures published throughout by measuring the performance
of variations to the original Deep Q-Network introduced by [Mnih et al., 2015] on the OpenAI Gym™ Atari 2600 suite. We measure the sample efficiency of three different models on a
selection of games in addition to introducing a novel variation to the original Rainbow-DQN algorithm. Our results indicate that sample efficiency is closely related agent's ability to represent
distributions of possible outcomes rather than expected returns.

# Results
*** Weights & Biases dashboard available <a href="https://app.wandb.ai/honne23/final_evals?workspace=user-honne23 ">here</a> ***
> Over the course of testing, it was difficult to consistently train the models as they were prone to
crashes due to memory error. This was because of the size of the experience replay buffer. In
order to get the models to learn on more difficult games such as Breakout and Demon Attack, a
larger pool of samples is required to learn the more complex policies of these games. In particular,
the internal structure of the prioritised replay buffer is that of a sum segment tree, which is a binary
heap data structure that maintains an array of data (in this case sampled transitions) that grows
with space complexity O(n) and another array of pointers to the nodes on the tree that grows
at a rate of O(2n) to maintain the binary relationships. While this enables faster time complexity
when updating priorities on the tree O(n log n), it does incur a significant cost to memory, often
crashing our GTX Titan Vs or overload RAM, as is evident by the figure on the previous page.
Considering that 4 stacked frames are preprocessed and passed into the model as a three dimensional matrix of shape (4, 84, 84), the full memory size after 30000 transitions is approximately (4*84*84*30000*64) / (8×10^9) ≈ 6.7GB assuming 64 bit floating point memory. In addition to the background
load on the card, this proved enough to overwhelm the on-device memory, especially in the case of DemonAttack.
However in the case of pong, although DDQN was unable to converge, our Quantile-Rainbow
agent converged significantly faster, within 70 episodes, indicating that the addition of n-step
learning and distributional returns greatly helped the agent extract more information from its internal representation of the environment. Indeed the success appears to be replicated in Breakout as the agent's average rewards appear to begin increasing after 10000 time-steps, however
this is an improper evaluation as we were unable to train DDQN for equally as long, due to
aforementioned failures. Although we have strong results for the simple pong environment,
more analysis is required.

# Conclusions
> Despite our agent's strong results on Pong, the remaining results appear to be inconclusive even
though Breakout looks promising. We ran multiple tests on each agent on each game, attempting
to train them for 100,000 frames each. Often in the more complex environments the system
resources would prove too much for the hardware at hand, echoing the claims of [Irpan, 2018].
Although when we observed the recorded videos of each agent, we found that quantile agent
did appear to be acting intelligently much earlier on than the DDQN agent, indicating that the
quantile returns provided a strong training signal earlier on during the training process, improving
sample efficiency drastically on Pong.
An interesting observation is that of the average loss graph over all models, there appears to be a
consistent spike earlier on in the training data followed by convergence to a stable state. This appears to be in line with the work of [Tishby and Zaslavsky, 2015] demonstrating the Information
bottleneck principal, this loss appears to initially spike as the neural network works to compress
the representation of the input into an efficient embedding, followed by a second phase where
the "network maximizes the conditional entropy of the layers subject to the empirical error constraint" [Tishby and Zaslavsky, 2015]. This is inline with our hypothesis indicating that one of
the primary bottlenecks to sample efficiency is in fact the efficacy of the neural network's ability to extract useful information from the representation, as the representation itself is already
rich but the information must be extracted with sufficient sophistication, but not so much as to
introduce excess bias.
In conclusion, although the claims of [Irpan, 2018] proved to hold true in practise, there is great
promise in new techniques that extract more useful information from the the neural network's
internal representations. For follow-up work we would further investigate this link between the
Information bottleneck principal and evaluate our results on a wider array of environments.


