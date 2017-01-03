---
layout: page
title: The Keras Tutorial: Introduction
categories: Neural_Networks Keras Tutorial
tags: Neural Networks Keras Tutorial
comments: True
---

#### Index

* What is Keras?
* Download and Install Keras
* Our first Neural Network

### What is Keras?

[Keras](https://keras.io/) is a high-level neural networks library written in Python and built on top of Theano or Tensorflow. That means you need one of them as a backend for Keras to work. I have been working with Neural Networks for a while, I have tried Caffe, Tensorflow and Torch and now I'm working with Keras. It is main advantage is the minimalism, it allows the creation of big networks with a few lines of code. It allows multi-input and multi-ouput networks, convolutional and recurrent neural networks, embeddings, etc. I'm really comfortable working with it so I thought it would be nice to write some paragraphs to explain how it works and the tricks I found. I hope this tutorial is helpful for new users.

### Download and Install Keras

Before we actually install Keras let's make our life easier and install the pip program.

{% highlight ruby %}
sudo apt-get install python-pip
{% endhighlight %}

Now we can easily install the dependencies:

{% highlight ruby %}
pip install numpy scipy scikit-learn pillow h5py
{% endhighlight %}

As I appointed in my first paragraph, Keras works on top of either Theano or Tensorflow, therefore we will need to install one of them. I work with Theano because it's easier to install in my case.

{% highlight ruby %}
pip install Theano
pip install keras
{% endhighlight %}

Now we have to specify that we want Theano as a backend ([link](https://keras.io/backend/)).

{% highlight ruby %}
nano ~/.keras/keras.json
{% endhighlight %}

In this file change the backend to Theano and the image ordering from 'tf' (which is the default option for Tensorflow) to 'th'. Note that the image ordering in Tensorflow is (width, height, channels) and in Theano is (channels, width, height). Finally, if you want to run Keras in your GPU you have to create a file '~/.theanorc' ('touch ~/.theanorc' in your command prompt) and include the following:

{% highlight ruby %}
[global]
floatX = float32
device = gpu0
 
[nvcc]
fastmath = True
{% endhighlight %}

If you want to use the CPU change 'gpu0' to 'cpu'. With this final steps we should have keras ready to work with Theano. 

### Our first Neural Network

First I will say that Keras has two different structures to create Neural Networks: the Sequential Model or the Functional API. We will work with the second one through the tutorial as it allows more freedom. If you have heard about the Graph Model I have to say that it has been removed (therefore it's deprecated) in the version 1.0 ([link](https://github.com/fchollet/keras/issues/2802#issuecomment-221314411)).

Let's start coding! Our first neural network is going to have a single input and a final dense layer (which will be the output). The example code can also be shown in the [Keras Models section](https://keras.io/models/model/) but we will go through each of the lines to understand better what we are doing.

{% highlight python %}
from keras.models import Model
from keras.layers import Input, Dense

a = Input(shape=(32,))
b = Dense(32)(a)
model = Model(input=a, output=b)
{% endhighlight %}

The first two lines are the imports. We can also avoid them and put the full name for each function and structure, like 'keras.layers.Input', but it's more readable if we put them.

The Functional API forces you to include an input layer. Here you have to specify the shape of your input (without the batch size). In this example we only have a 1D input of 32 values, i.e., the shape of your input would be (batch size, number of features) but in this layer you only have to include (number of features). The comma after the value 32 is also mandatory in some cases or it can throw errors. Anyway, your full input layer is stored in the variable 'a'.
Now we include a Dense layer by calling the function and providing the number of neurons for that layer (in this case 32). The next step is to stack the output layer or dense layer on top of the input layer, i.e., we have to connect them. In this type of model we do this by providing the variable of the last layer at the end of the new layer (check the 'a' between parenthesis after the Dense layer).

Finally, we have to instantiate the Model or creating a container for it. We can do this with the Model function, providing the input and output. In this case providing the variables of the input and output layers. We can also provide a Python list for multi-input and output.
