---
layout: post
title: Control your hardware use (CPU, GPU) with Keras
categories: Keras Tutorial Python GPU CPU Hardware
tags: Keras Tutorial Python GPU CPU Hardware
comments: true
---

In Deep Learning projects, where I am used to occupy a great amount of memory, I found very useful to have a way of measuring my use of the space in RAM and VRAM (GPU memory). Here I will provide some tools to do this, although they may be better options I found that these solutions are easy to put in practice.

Theano version: 0.8.2
Tensorflow version: 0.12

## RAM memory

To control the use of RAM we make use of the psutil python package, you can install it via pip:

{% highlight ruby %}
pip install psutil
{% endhighlight %}

Then, we will create a function where we allocate memory in RAM. In my case, I created a numpy ndarray. The first line of the code is mandatory, then you insert your code and finally we obtain the RAM use. I formatted the amount so that it is human-readable and presented it in GB.

{% highlight python %}
import psutil
import numpy as np

def foo():
     p = psutil.Process()
     x = np.ones((1200,960,3))
     values = psutil.virtual_memory()
     used = values.used >> 20
     print('RAM: {}'.format(float(used/1000.0)))

foo()
>>> RAM: 0.254
{% endhighlight %}

With these lines we will have the amount of RAM employed printed in the screen.

## VRAM memory

VRAM is the memory inside a GPU where the neural network model and data mini-batches are stored. We can see the occupied space using the command "nvidia-smi" in a terminal. However, by default both Theano and Tensorflow preallocate memory. Thus, we do not know the real space that we are using.

Note: you can use "watch nvidia-smi" so that the output is refreshed automatically.

#### Solution in Theano

Go to the "theanorc" file and add the following:

{% highlight ruby %}
[lib]
cnmem = 0
{% endhighlight %}

With this change applied we will see that now we can actually measure the real space occupied by our model and data.
