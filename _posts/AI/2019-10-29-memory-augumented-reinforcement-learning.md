---
layout: post
comments: true
title: "Memory Augumented Reinforcement Learning"
date: 2019-12-08 00:00:00
tags: memory reinforcement-learning
cate: AI
---

> Memory is an important part for human intelligence. Augumenting deep learning especially deep reinforcement learning with a memory module inspired by human memory could be a good idea for the interdiscipline research.

<!--more-->
As one of the most important capabilities of animals, memory is the process of storing, encoding and retrieving information and crucial for learning and intelligence. At the same time, artificial intelligence such as deep learning learns everything end to end without a representation of memory module. Here we summarize some attempts to augument deep reinforcement learning with a memory module.  


{: class="table-of-content"}
* TOC
{:toc}

## Memory Modules
### Model Free Episodic Control
In deep reinforcement learning, neural networks cannot latch onto successful experiences rapidly because of the slow gradient-based update and thus needs a lot of training data. However, human are good at learning from and repeat the successful strategies because of the support of hippocampus and medial temporal lobe structrues. As a result, a memory module recording rewarding experiences could enable the agent to learn to get higher rewards in a much shorter time. 

In model-free episodic control, the memory module is represented by a table whose key is the state-action pair and value is the maximum discounted return. Each time we need to take the action, we refer to the Q value table. If one pair s-a is missing in the table, we use k nearest neighbors to do the estimation. 
<br/>
<br/>
![Model Free Episodic Control]({{ '/assets/images/model-free-episodic-control.png' | relative_url }})
<center> <i> Fig. 1 Algorithm of Model Free Episodic Control</i></center>
<br/>

The biggest benefit is of course the fast learning. Besides that, I think a memory module also allows the value estimation for different states to be non-intefering. For example, in DQN, the update of parameters based on state $$s_i$$ will change the state value for $$s_j$$ more or less.

### Neural Episodic Control
Neural episodic control uses a similar tabular memory module to store $$Q(s,a)$$. However, instead of storing the maximum discounted return, the memory module in NEC stores the expected return just like DQN. Besdies that, in contrast to representing the state via VAE or random network projection which are used in model free episodic control, NEC learns a representation in an end-to-end way with the help of a differentiable memory module called differentiable neural dictionary (DND). 

<br/>
<br/>
![NEC]({{ '/assets/images/NEC-archi.png' | relative_url }})
<center> <i> Fig. 2 Architecture of NEC </i></center>
<br/>

The architecture of NEC is shown above. It could be broken down into 3 parts. The first one is a convolutional neural network which maps $$s$$ to $$h$$. The second part is DND whose key is $$h$$ and value is $$Q(s,a)$$. For each action $$a$$ in action space, it has a separate DND memory $$M_a$$. The thrid part takes $$h$$ and memory modules in and outputs an estimated state-action value. This process is similar to the way used in MFEC.
<br/>
<br/>
![NEC-pseudocode]({{ '/assets/images/NEC-pseudocode.png' | relative_url }}){: style="width: 60%;" class="center"} 

<center> <i> Fig. 3 Algorithm of Neural Episodic Control </i></center>
<br/>

The update of NEC agent includes the read and write operations on DND and update of parameters of the convolutional neural network. The end-to-end training may help the NEC agent learn a better representation that contains relationships between the observation and $$Q(s,a)$$.
