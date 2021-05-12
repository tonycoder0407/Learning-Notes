# A glimpse of DL

## Chapter 6. Introduction to Artificial Neural Networks

Linear Threshold Unit LTU

Accept inputs => multiply weight => weighted sum => step function => output

Multiple LTU will have multiple outputs and create an output layer

This solves linear separable problem

Why are neural network become deeper rather than wider?

Wider will be good at memorizing but not generating

At an infinite number of dimension all the problems will be Linear Separable 

Feature Engineering: A case-by-case analysis problem

So we don't care about how to extract the features but we pay more attention to the network

Using the hidden layers to tell the machines to learn the features themselves

Then.....

Why do you need deep learning? You have simply one layer of input and multiple hidden ones you don't even need to care to solve the problem.

And.......

Now we introduce Neural Networks: A better performance algorithm than others

Why don't we continue to use former architecture?

With more level of deep hidden cells you need to provide a lot of weight and this weight will reflect the loss of precision back and...... 

Not this architecture can't meet our need but it's too complicated to use

And we try to simplify the process and create such model

What we focus on moves from feature engineering to methods to simplify the base model "Deep Feed Forward"

Convolution Neural Networks CNNs

Feed-Forward Neural Network is prone to overfitting due to the presence of many parameters within the network to learn

Most commonly applied to analyzing visual imagery

Also known as shift invariant or space invariant artificial neural networks (SIANN)

Pre-deep learning era

We have an image now. => Raw pixels => Feature: car, bus, *monument*, flower

=> edge detector: car, bus, *monument*, flower 

=> SIFT/HOG => static feature extractions and learning weights of classifier

Using DL:

Why not flatten the image into a vector and pass into the Feed-Forward Neural Network or MLP?

e.g. 1024*1024 pixels

We will have more than 1,000,000 inputs and use this to calculate

This too detailed info will do too much waste of calculation

And we will use a summary of info, to extract part of it

***

One Dimension

Use a multi dimension vector and get a weighted sum => Input

e.g. We can have such weight example for current speed as this below:

0.01 0.01 0.02 0.02 0.04 0.4 0.5

We use such 7 values of speed with weight to describe the current speed

And also, we can use some weights to describe the significance of one pixel

Using this method, Neural Network will do linear calculations which it is best at

***

Two dimensions 

We can get a weight sum of 3\*3 point matrix and turn it into a 1\*1 one

And..... By moving one pixel we can know that 5\*5 point matrix can be converted to a 3\*3 one

We can also use Gaussian Blur to do this process

What we use to do convolution is called as a filter or a kernel

***

2D convolution with 3D filter

Always we may use RGB 3 types of number to describe an image

And we will get 3 feature map and reunite them as a new image

***

Output Dimensions

When we do convolution, always we may find that when 5\*5 pixels will create only 3\*3 pixels of summary

To solve this problem we introduce valid padding and same padding

Valid padding won't do any other action

Same padding will create outside zeros to make sure output have the same size as the input

***

Sparse Connectivity and Weight Sharing

In convolution network there will have much less connections for we don't connect all the nodes

And as for a 3*3 kernel we will have only 9 weights ==> Much less than full connection network

***

Pooling Layer

We can also use subsampling to further simplify the input

* Max Pooling
* Average Pooling
* Min Pooling

> The more you know, the faster you die.															-- Dr.Gui

***

Fully Connected Layer in LeNet

https://en.wikipedia.org/wiki/LeNet

***

CNN Architectures

* LeNet: Average Pooling
* AlexNet: Using Max Pooling, Drop Out(At Full Connection Part)
  * The first one to defeat the traditional method
* ZFNet: Make Kernel Visible
* GoogleNet: Inception V1
  * Using Convolution on Kernels
  * Top-5 Error rate of 6.67% ==> Human Level!

> We need to go deeper! 																						-- *Inception*

* VGG
  * Only 3*3 convolutions
  * But 16 filters!
  * Trained on 4 GPUs for 2-3 weeks
* ResNet
  * Jump over level and spread input forward

How do we use a pretrained network to do our own business?

Check the book **

Two 3\*3 filters (20 inputs) = a 5\*5 filter(26 inputs)

Three 3\*3 filters (30 inputs) = a 7\*7 filter(50 inputs)

How to calculate the input: filter x multiply filter y and plus one (3*3 + 1 = 10)

***

Recurrent Neural Networks RNNs

Machine Learning focus on how to find
$$
y = f(x)
$$
But what does RNN do?
$$
y = f(x) ==> y_t = f(x_t, y_{t-1})
$$
One cell can be used over and over and over again

***

LSTM Networks

Break up as two lines

* Long term
* Short term

INPUT =>

=> Forget gate layer => How much of long term memory stays ===>

=> Input gate layer => How much this input will enter long term line ===>

===> Update the memory

==> Output gate layer

====> Output or enter next short term line

***

Gate Recurrent Unit GRU

Associate the forget gate and part of the input gate ==> reduce the parameters

Other Variants

* Depth Gated RNNs
* Clockwork RNNs

Attention and Augmented Neural Turing Machine

***

Autoencoders AE

We can use this function and reduce the dimensions of vectors and would be able to return the similar vector back

***

Denoising Autoencoder

Origin => Noise added => ENCODER => Code => DECODER => Output ==> Similar as origin

***

PCA and AE

3D Nonlinear Swiss Roll 2D linear manifold in 3D

AE can do non linear dimension reduction

PCA can only do linear dimension reduction

***

Word Embedding

One Hot Representation vs. Distributed Representation

***

Word2Vec

If words have no specific meaning if not connected with others

Something like

* Eatable
* Red
* Round
* Fruit
* .....

If we regard this thing as an apple

Then the words listed should also mean "apple"

This structure is an AE

Also, we can also decode "apple" to that list

Using CBOW and Skip-gram