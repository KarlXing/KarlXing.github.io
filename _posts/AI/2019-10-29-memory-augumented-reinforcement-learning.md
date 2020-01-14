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

|Algorithm|Memory Type|Goal|Features|
|:--:|:--:|:--:|:--:|
|MFEC|&nbsp;Tabular memory&nbsp;|&nbsp; Fast Learning &nbsp;|Store Maximum Return|
|NEC|Tabular memory|Fast Learning|End-to-End training for better representation|
|EVA|Replay Buffer|Fast Learning|&nbsp;Trajectory-centric Estimates + Parametric Estimates&nbsp;|
|NTM|Matrix|Partial Observation|Attentional read and write based on addressing mechanisms|
|DNC|Matrix|Partial Observation|New attention mechanism (temporal links & memory usage record)|
|Neural Map|2D Memory Image|Partial Observation|Simplified DNC without complicated attention mechanism, but requires oracle of current position|
|MERLIN|
{:.mbtablestyle}

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

### Ephemeral Value Adjustments
Both NEC and MFEC utiliz dictionary-like memory modules to store and retrieve $$Q$$ values to allow faster value estimation. However, Ephemeral Value Adjustments didn't drop the parametric neural network value estimation. Instead, it proposed to rapidly adapt to experience in the replay buffer by shifting the value predicted by the neural network with an estimate of the value function over the trajectories starting from similar states of the current state. 

<br/>
![EVA-architecture]({{ '/assets/images/EVA-archi.png' | relative_url }})

<center> <i> Fig. 4 Architecture of Ephemeral Value Adjustments. The left figure is the trajectory-centric planning over memories in the replay buffer. The right figure is adjusting value estimation for action selection. </i></center>
<br/>

As demonstrated in the left figure above, after getting the embedding $$e$$ of the current state, we find similar states $$e'$$ from the replay buffer and get fixed-length trajectories starting from these states. With these traces, we could get value estimates via trajectory-centric planning (TCP) algorithm:
![EVA-tcp]({{ '/assets/images/EVA-tcp.png' | relative_url }}){: style="width:45%;" class="center"} 

where $$Q_{NP}$$ and $$V_{NP}$$ are trajectory-centric value estimates. $$Q_\theta$$ is the value estimate from the parametric neural network. $$T$$ is the maximum length of the trajectory. The final value state estimate is the weighted average over $$Q_{NP}$$ and $$Q_{\theta}$$ with a weight parameter $$\lambda$$.

### Neural Turing Machine
Although some work like MFEC and NEC utilized memory modules for information storage and retrieval, the memory implementations are still limited. For example, the information stored is merely $$Q$$ values and the writing operation is justing appending. This is not a surprise since the goal of MFEC and NEC is latching on successful experiences faster. Of course, we could also use memory to remember much more information. But what to read and write is a question then. 

NTM proposed to use attentional processes to read and write memory where attention is calculated based on read and write heads from the controller. The whole system composed of neural network (controller) and the external memory is differential and thus could be trained via graident descent. 

![NTM-archi]({{ '/assets/images/NTM-archi.png' | relative_url }}){: style="width:50%;" class="center"} 
<center><i>Fig 5. Architecture of NTM. The controller connects the external world and the memory block. The interaction between the controller and memory is via attention from read and write heads. </i></center>  
<br/>

The most important part on memory access is the addressing mechanism which is demonstrated in the figure below. **Content-addressing** attention $$w^c_t$$ is defined based on embedding/key/content similarities. The interpolation decides how much we count on attention from last step and attention from content addressing at the current step. After that, the convolutional shift allows the attention weights to shift across locations which is designed for **location-based addressing**. Finally, the sharpening is used for potential issues of dispersion attention over time. 



![NTM-addressing]({{ '/assets/images/NTM-addressing.png' | relative_url }}){: style="width:100%;" class="center"} 
<center><i>Fig 5. Addressing mechanism of Neural Turing Machine </i></center>

### Differntiable Neural Computer
DNC is the successor of NTM. The difference is on addressing mechanisms.  
1. As the most popular and common addressing mechanism, content-based addressing is still kept. However, location-based addressing is droped here. 
2. Instead, a $$N \times N$$ temporal link matrix $$L$$ is used to record sequential information. $$L[i,j]$$ is close to 1 if $$i$$ was written after $$j$$ and close to 0 otherwise. 
3.  Besides that, the 'usage' of each location is also represented as a number between 0 and 1 so that less used locations could be reallocated for writing. The write operation will increase the usage and reading will decrease the usage. ***(I'm not sure if this is the best way to define usage.)***


### Neural Map
Neural map used spatially structured 2D memory image $$M$$ for information storage. The information stored at  $$M^{(x,y)}$$ represents what the agent met when it reached position $$(x,y)$$ of the environment. As a result, it requires oracle to provide the position of the agent and the environments are limited to 2D or 3D tasks. Like DNC, it also defines differentiable read and write operations over the memory. However, because we know how $$M$$ is designed , the attention mechanism in DNC is much simplified. For reading, neural map defines global read which summarizes information about the entire map and conetext read which retrieve information from the map based on current state via soft attention. For writing, $$w_{t+1}^{x_t, y_t}$$ is the output of a neural network which takes global read, context read, state embedding and memory as input.


## References
1. Blundell, C., Uria, B., Pritzel, A., Li, Y., Ruderman, A., Leibo, J., Rae, J., Wierstra, D., Hassabis, D. (2016). [Model-Free Episodic Control](https://arxiv.org/abs/1606.04460)  
2. Pritzel, A., Uria, B., Srinivasan, S., Puigdomènech, A., Vinyals, O., Hassabis, D., Wierstra, D., Blundell, C. (2017). [Neural Episodic Control](https://arxiv.org/abs/1703.01988)    
3. Hansen, S., Sprechmann, P., Pritzel, A., Barreto, A., Blundell, C. (2018). [Fast deep reinforcement learning using online adjustments from the past](https://arxiv.org/abs/1810.08163)  
4. Graves, A., Wayne, G., Danihelka, I. (2014). [Neural Turing Machines](https://arxiv.org/abs/1410.5401)
5. Graves, A., Wayne, G., Reynolds, M., Harley, T., Danihelka, I., Grabska-Barwińska, A., Colmenarejo, S., Grefenstette, E., Ramalho, T., Agapiou, J., Badia, A., Hermann, K., Zwols, Y., Ostrovski, G., Cain, A., King, H., Summerfield, C., Blunsom, P., Kavukcuoglu, K., Hassabis, D. (2016). [Hybrid computing using a neural network with dynamic external memory Nature  538(7626), 471-476.](https://dx.doi.org/10.1038/nature20101)
6. Parisotto, E., Salakhutdinov, R. (2017). [Neural Map: Structured Memory for Deep Reinforcement Learning](https://arxiv.org/abs/1702.08360)

