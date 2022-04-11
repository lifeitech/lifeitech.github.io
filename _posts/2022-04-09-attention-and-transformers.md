---
title: Attention and Transformers
date: 2022-04-11 22:20:00 +0800
categories: [Data Science]
tags: [nlp, transformer]
image:
  src: /assets/imgs/transformers/transformers.jpg
  alt: "Attention and Transformers"
  width: 800
  height: 500
math: true
---

> In recent years, Transformer-based models are trending and are quickly taking up not only the field of NLP, but also computer vision and many other fields in AI. In this post, we give a tutorial on Transformer, and talk about several state of the arts models based on it, including GPT and BERT. 

## The Attention Model
Even with [LSTM](https://lifei.tech/posts/rnn/#long-short-term-memories-lstm) units, problems remain for recurrent neural network models:

1. Passing data forward through an extended series of recurrent connections leads to loss of information and to difficulties in training. 
2. Moreover, inherently sequential nature of recurrent networks inhibits the use of parallel computational resources. 

Attention [[1]](#1) model (Figure 1) takes a different approach. Each output element $y_i$ in the output sequence $Y=y_1\cdots y_n$ is computed directly as a function of $x_1,\ldots,x_i$, the input sequence up to element $i$. Namely, $y_i$ is connected to $x_1,\ldots,x_i$ by ***direct*** connections. Thus, computation at $y_i$ is independent of computations for other outputs, so that they can be performed in parallel.

![Self-Attention Layer](/assets/imgs/transformers/attention.png){: width=500}
_Figure 1: Self-Attention Layer_

Specifically, output $y_i$ at position $i$ is a weighted sum of input $i$ with all previous inputs, $x_1,\ldots,x_i$:

$$
y_i = \alpha_{i1}x_1 + \alpha_{i2}x_2 + \cdots + \alpha_{ii}x_i.
$$

The weight $\alpha_{ij}$ is a normalized score $s(x_i,x_j)$ for $x_i$ and $x_j$, reflecting their similarity, the simplest of which is the dot product. Specifically,

$$
\alpha_{ij} = \frac{ \exp(s(x_i, x_j)) }{\sum\limits_{j'=1}^i\exp(s(x_i,x_{j'}))} \qquad \forall j\leq i.
$$

To allow the possibility for learning, instead of summing raw input values in the above equation for calculating $y_i$, each input $x_i$ is first mapped to three vectors via three weight matrices:

$$
\begin{align}
k_i &= W^Kx_i,\\
q_i &= W^Qx_i,\\
v_i &= W^Vx_i.
\end{align}
$$

And then, 
1. the key vector $k_j$ replaces $x_j$ in $s(x_i,\textcolor{red}{x_j})$ for $j\leq i$; 

2. the query vector $q_i$ replaces $x_i$ in $s(\textcolor{red}{x_i},x_j)$;

3. the value vector $v_i$ replaces $x_i$ in 

$$
y_i = \alpha_{i1}x_1 + \alpha_{i2}x_2 + \cdots + \alpha_{ii}\textcolor{red}{x_i}.
$$

See figure below for an illustration when calculating $y_3$.

![Illustration for Self-Attention Layer; Transformer](/assets/imgs/transformers/attention-detail.png){: width=500}
_Figure 2: Illustration for calculating the third output element $y_3$ in an attention model._

### Practical Considerations
In practice, since the score $s(x_i, x_j)= q_i \cdot k_j$ can be very large ($+\infty$) or small ($-\infty$), it can cause overflow or underflow problem when taking the exponentials in calculating the normalized weight $\alpha_{ij}$. To mitigate this issue, one may divide the dot product by the square root of the dimensionality of the query and key vectors:

$$
s(x_i, x_j) = \frac{q_i\cdot k_j}{\sqrt{d_k}}.
$$

Due to the parallel nature of the model, we can concatenate all the input embeddings into a single matrix $X$, and multiply it by the key, query and value matrices to get matrices containing all the $k,q$ and $v$ vectors.

$$
\begin{align}
Q &= W^QX\\
K &= W^KX\\
V &= W^VX.
\end{align}
$$

Finally, we can reduce the entire self-attention step for an entire sequence to the following computation:

$$
\mathrm{SelfAttention}(X) = \mathrm{softmax}\left(\frac{QK^T}{\sqrt{d_k}}\right)V.
$$

This brings up a new problem though. The calculation of matrix product $QK^T$ results in a score $s(x_i,x_j)$ for each $x_i$ and each $x_j$ in input sequence $(x_1,\ldots,x_n)$, including those $x_j$ for $j>i$. In language modeling, our task is to predict the next word, so we may only use $x_1,\ldots,x_i$ as inputs to produce output $y_i$. Using $x_{i+1},\ldots,x_n$ to predict $x_{i+1}$ would not lead to meaningful learning. To fix this, the elements in the upper-triangular portion of the comparisons matrix are set to $-\infty$, thus eliminating any knowledge of words that follow in the sequence.

## Transformers
The Transformer [[2]](#2) model (Figure 3) is built on top of the Attention model, to acheive more potentials.

1. First, in the transformer block, input is added to the output from the self-attention layer and normalized. Then it goes through a feedforward layer and then again residual connection and normalization. The Transformer block can be stacked and combined with other layers, to form a final model.

2. The self-attention layer in Figure 3 can be replaced with multi-head attention layer (Figure 4), in which you map an input to several self-attention layers in parallel, each with its own parameters, and then concatenate them and map the result to an output with the original dimension. The motivation for this is to let those self-attention layers capture different relationships among the inputs $x_1,\ldots,x_n$.

3. Finally, to further capture the information about the sequential order of the inputs, some positional embeddings may be added to the input sequence.

![Transformer block](/assets/imgs/transformers/transformer.png){: width=500}
_Figure 3: Transformer block_

![Multi-head attention](/assets/imgs/transformers/multihead-attention.png){: width=500}
_Figure 4: Multi-head attention_

### Generative Pre-Training (GPT)
The GPT [[3]](#3) model was proposed by OpenAI in 2018. It is a Transformer model with **generative pretraining** and **discriminative fine-tuning**. Namely, it first trains on unlabeled text data to predict the next word, with NLL as the loss. Then for downstream tasks, you add a final output layer to the model, and you train on labeled data (with e.g. NLL as loss) to fine-tune the parameters of the model. 

The point is to take advantage of the large amount of cheap unlabeled data available in the wild, and to reduce the reliance on human-labeled data. The [GPT-2](https://openai.com/blog/better-language-models/) [[4]](#4) model architecture is the same with GPT, just much larger. The authors examined performance of the model on discriminative tasks without the fine-tuning step, which is called "zero-shot".

### BERT
BERT [[5]](#5), which stands for "Bidirectional Encoder Representations from Transformers", is built upon GPT, with two improvements.

1. It uses **bidirectional Transformer** blocks, rather than unidirectional Transformer blocks used in GPT (i.e. Figure 3). Namely, each unit in Figure 1 connects not only to previous and current words, but to all the input words. The reason for this is that, by establishing more direct connections to the inputs, the model could have more capacity.
2. Pretraining consists of **two tasks**: 

    (1) masked language modeling (Masked LM), where some random tokens are masked and the model learns to predict those tokens. This is to avoid failure of plain language modeling, since in such setting the bi-Transformer block can see the word it is going to predict, so training with such objective leads to collapse, as we have discussed earlier.

    (2) next sentence prediction (NSP). This is a binary task. Given two sentences $A$ and $B$, predict if $B$ is the next sentence that follows $A$. The purpose is to train a model that understands sentence relationships, so that it can have better performance in downstream tasks like Question Answering (QA) and Natural Language Inference (NLI).

To get a more detailed sense of the BERT model, you can read the following quote from the paper [[5]](#5):


> To generate each training input sequence, we sample two spans of text from the corpus, which we refer to as "sentences" even though they are typically much longer than single sentences (but can be shorter also). The first sentence receives the $\texttt{A}$ embedding and the second receives the $\texttt{B}$ embedding. $50\%$ of the time $\texttt{B}$ is the actual next sentence that follows $\texttt{A}$ and $50\%$ of the time it is a random sentence, which is done for the "next sentence prediction" task. They are sampled such that the combined length is $\leq512$ tokens. The LM masking is applied after WordPiece tokenization with a uniform masking rate of $15\%$, and no special consideration given to partial word pieces.
> 
> We train with batch size of $256$ sequences ($256$ sequences $*$ $512$ tokens = 128,000 tokens/batch) for 1 million steps, which is approximately $40$ epochs over the $3.3$ billion word corpus. We use Adam with learning rate of 1e-4, $\beta_1=0.9$, $\beta_2=0.999$, L2 weight decay of $0.01$, learning rate warmup over the first $10,000$ steps, and linear decay of the learning rate. We use a dropout probability of $0.1$ on all layers. We use a $\texttt{gelu}$ activation rather than the standard $\texttt{relu}$, following OpenAI GPT. The training loss is the sum of the mean masked LM likelihood and the mean next sentence prediction likelihood.
> 
> For fine-tuning, most model hyperparameters are the same as in pre-training, with the exception of batch size ($16,32$), learning rate (e.g. 5e-5), and number of training epochs ($2,3,4$).
> 
> ...... **The core argument of this work is that the bi-directionality and the two pre-training tasks account for the majority of the empirical improvements**.


## Summary & What's Next

So here we are. We have studied all the common models in NLP up to now, from the simplest [n-gram](https://lifei.tech/posts/nlp-prob-ngram/) model, to [word2vec](https://lifei.tech/posts/embedding-and-word2vec/), [Hidden Markov Model](https://lifei.tech/posts/sequence-labeling/#hidden-markov-model-hmm), [Conditional Random Field](https://lifei.tech/posts/sequence-labeling/#conditional-random-field-crf), [recurrent neural networks](https://lifei.tech/posts/rnn/), to Transformer-based models like GPT and BERT in this post. Transformer models achieve better results because more direct connections between inputs and outputs lead to more efficient learning. Not only are they the state-of-the-art models in NLP, but computer vision, for which convolutional neural networks were once dominant, also sees the trend of transitioning to Transformer-based models. See e.g. ViT [[6]](#6) and MAE [[7]](#7). 

Another noticeable trend in AI research is that, big companies are training ever bigger models with billions of parameters. See e.g. Microsoft's Florence [[8]](#8) or Google's Pathways [[9]](#9) model. The cost of training such models is astronomical, which often requires GPU resources that cost hundreds or thousands of millions of dollars. This is simply prohibitive for any individual. Gone are the days when someone can just sit in front of a desk and implement and run a state-of-the-art model on his or her laptop or on a rented machine in the cloud. 

While pushing the boundary of neural network models by boosting model size is perfectly legitimate, there are many other important problems to consider. One fundamental problem is, how do neural network models actually work? What do they learn? What is the function of each parameter? Second, how should a model acquire robust generalization ability from less data, just as humans do? And is neural network really the ultimate paradigm for Artificial Intelligence? While current models may still have limited capacity, I'm optimistic that, we should see better and better AI models coming out in the future, and one day machines can have enough intelligence to handle tasks and jobs we do today.


## References

<!-- attention -->
<a id="1">[1]</a> Bahdanau, Dzmitry, Kyunghyun Cho, and Yoshua Bengio. "Neural machine translation by jointly learning to align and translate." _arXiv preprint arXiv:1409.0473_ (2014).

<a id="2">[2]</a> Vaswani, Ashish, et al. "Attention is all you need." _Advances in neural information processing systems_ 30 (2017).

<!-- GPT -->
<a id="3">[3]</a> Radford, Alec, et al. "Improving language understanding by generative pre-training." (2018).

<!-- GPT-2 -->
<a id="4">[4]</a> Radford, Alec, et al. "Language models are unsupervised multitask learners." _OpenAI blog 1.8_ (2019): 9.

<a id="5">[5]</a> Devlin, Jacob, et al. "Bert: Pre-training of deep bidirectional transformers for language understanding." _arXiv preprint arXiv:1810.04805_ (2018).

<!-- ViT -->
<a id="6">[6]</a> Dosovitskiy, Alexey, et al. "An image is worth 16x16 words: Transformers for image recognition at scale." arXiv preprint arXiv:2010.11929 (2020).

<!-- MAE -->
<a id="7">[7]</a> He, Kaiming, et al. "Masked autoencoders are scalable vision learners." arXiv preprint arXiv:2111.06377 (2021).

<a id="8">[8]</a> Yuan, Lu, et al. "Florence: A New Foundation Model for Computer Vision." arXiv preprint arXiv:2111.11432 (2021).

<a id="9">[9]</a> Chowdhery, Aakanksha, et al. "PaLM: Scaling Language Modeling with Pathways." arXiv preprint arXiv:2204.02311 (2022).