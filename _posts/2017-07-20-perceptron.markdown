---
layout: post
title:  "Perceptron"
date:   2017-07-20
excerpt_separator: <!--more-->
---

The perceptron is one of the simplest Machine Learning algortithms, but also one of the mnost important. It is a building stone of neural networks and deep learning, so it is certianly worth investigating.
<!--more-->

The general idea is to simulate the connection between neurons. Before we come to the perceptron we should take a look at those. For our purpose it is enough to imagine the neurons as cells that receive electrical signals through synapses and send singals out themselves. Now this is quite general, but with a few additions this will give us a nice Machine Learning algorithm.

The learning process in the brain only affects the synapsis. The more often it is used, the stronger it gets and with this increases the strength of the transported signal. The signals are received in the neuron where they are added until a threshold is reached and the neuron itself fires, thus sending out a new signal.

[comment]: <> TODO: Add picture

This is the generla idea we wanto to capture in our algorithm. We see that we need three different parts, neurons which receive input and synapsis which transport it and an input in which we can give our data. Since we ant to simulate neurons we need to give out input in th form of neurons. So for every feature we have a node whic hreceives the numerical value and we pretend that this is the signal of a neuron. This input needs to be added together according to the strength of the synapsis. Let us call \\(x_j\\) the input in \\(i\\) and \\(w_{ij}\\) the weight of the synapsis connecting input \\(i\\) and neuron \\(j\\). The we can wirite the input in neuron \\(j\\) as

  $$h_j = \sum_{i=1}^{N} w_{ij} x_i.$$

We can numerically compute this for every neuron. But if we think about how neurons work, they do not just add all input, but they need to cross a threshold to fire. The easiest way is to just define the output of neuron \\( j\\) \\(y_j\\) as the result of an activation function \\(g(h_j)\\)

  $$ y_j = g(h_j) = \begin{cases} 1, h_j > 0\\ 0, h_j \leq 0 \end{cases}.$$

Now we have a proper output as well as a way to calculate it. Using some Linear Algebra we even find a nice representation of the calculation of \\(h_j\\) by noting that the weigths \\(w_{ij}\\) form a matrix \\(\pmb{W}\\) and the \\(x_i\\) and \\(h_j\\) vectors \\(\vec{x}\\) and \\(\vec{h}\\). We can the rewrite this to

  $$ \vec{h} = \pmb{W}^T \vec{x}, \\ \vec{y} = g(\vec{h}),$$

where \\(^T\\) denotes the transpose of a matrix and a slight abuse of notation for the function \\(g\\). This may seem more complicated, but using a lib like NumPy makes the whole calculation faster if we use this vector notation.

Now let us imagine we have an input of all \\(0\\)'s. What would happen to the output? All sums would be \\(0\\) and no neuron would fire. This may be not what we want, maybe one of the neurons should fire. One way to rectifie this is by changing the activation function to activate at anoter value instead of \\(0\\). This is a possible solution, but the implementation of the learning will be hard, so instead we add a bias \\(b_j\\) which gets added to the input \\(h_j\\). This is easiest done by using an additional input node, which will always have the same value \\(-1\\). So we do not need to change our model, we just have to add one extra constant feature to our data. This will be very useful in the training of the algorithm.

For the training we need test data. For this test data we need to konw the targets \\(\vec{t}\\), since the perceptron is a supervised method. Now to train our network we have to update the weights, since the inputs are fixed, the activation function is fixed and the biases are regulated with their weights. To update the weight we have to consider the difference between the target and the output of any given neuron. This leads to a change in the weight of

  $$ \Delta w_{ij} = - \eta (y_j - t_j) x_i .$$

\\(\eta\\) is calles the learning rate. The higher you set it the faster the perceptron will converge to its final state, but a too high learning rate will not give you the best solution, so it is a tradeoff between accuracy and speed. A typical learning rate is of the order of \\(0.01\\). In our vector notation this can also be written as

  $$ \Delta \pmb{W} = - \eta*

And thats it, now you can use the perceptron. The algorithm looks something like this:

[comment]: <> TODO: Go into more detail about the inpu


{% highlight python %}
import numpy as np


class Perceptron:
    """
    Class for the perceptron machine-learning algorithm. It describes a neural
    network consisting of one layer of neurons and one input layer.
    """

    def __init__(self, n_in, n_out, learning_rate=0.01):
        """
        Initializes a perceptron network using a gradient descent method.

        :param n_in: number of input nodes
        :param n_out: number of output nodes
        :param learning_rate: learning-parameter for gradient descent
        """
        self.n = n_in + 1
        self.m = n_out
        self.w = np.random.normal(0.0, 0.1, (self.n, self.m))
        self.eta = learning_rate

    def predict(self, data):
        """
        Predicts the result for input data.

        :param data: input data
        :return: predictions for the data
        """
        # Create the list for the predictions
        prediction = []
        # Add the bias
        features = np.concatenate((data, -np.ones((data.shape[0], 1))), axis=1)
        for dat in features:
            h = np.dot(self.w.transpose(), dat)
            # Activation function
            prediction.append(np.where(h > 0, 1, 0))
        return np.array(prediction)

    def fit(self, data, target, T):
        """
        Fits the perceptron network to the given data using gradient descent.

        :param data: numpy array of the training data
        :param target: numpy array of the targets corresponding to the data
        :param T: number of iterations
        """
        training = np.concatenate((data, -np.ones((data.shape[0], 1))), axis=1)
        for i in range(0, T):
            for i in range(training.shape[0]):
                dat = training[i]
                h = np.dot(self.w.transpose(), dat)
                y = np.where(h > 0, 1, 0)
                self.w -= self.eta * np.dot(dat.reshape((dat.shape[0],1)),
                          (y - target[i]).reshape((y.shape[0],1)).transpose())
{% endhighlight %}

And a short example for the logical AND function

{% highlight python %}
data = np.array([[0, 0], [0, 1], [1, 0], [1, 1]])
target = np.array([[0], [0], [0], [1]])
p = Perceptron(2, 1)
p.fit(data, target, 100)
print(p.predict(data))
# >> [[0], [0], [0], [1]]
{% endhighlight %}
