[![Build Status](https://travis-ci.org/jaideepcoder/AlarmInference.svg?branch=master)](https://travis-ci.org/jaideepcoder/AlarmInference)

# AlarmInference

This file contains boilerplate code to explain how to use the inference algorithms implemented in order to run custom
queries on the ALARM Bayesian Network.

The CPDs are stored in Pandas DataFrames. Here is an example for LVEDVOLUME node.

````python
cpd = bn.cpd['LVEDVOLUME']
print(cpd)
````
With these DataFrames, one for each node, all the knowledge is encoded in the Python Object Network instantiated with bn.

We can obtain the probability for any node in the following methods:

````python
p = bn.get_probability('LVEDVOLUME')
print(p)
````
This statement can be modified to perform more meaningful analysis.

````python
p = bn.get_probability('LVEDVOLUME', evidence={'HYPOVOLEMIA': 'TRUE'})
print(p)

p = bn.get_probability('LVEDVOLUME', evidence={'HYPOVOLEMIA': 'TRUE', 'LVFAILURE': 'TRUE'})
print(p)

p = bn.get_probability('LVEDVOLUME', evidence={'HYPOVOLEMIA': 'TRUE', 'LVFAILURE': 'TRUE'}, value='HIGH')
print(p)
````
Any node can be sampled using the following step. The node needs to be sampled in descending order
i.e. parents need to be sampled first. This is achieved using recursion. The parameter r is provided for testability
to be able to reproduce results as sampling is a uniformly random step.

````python
sample = bn.sample('LVEDVOLUME', given={'HYPOVOLEMIA': 'TRUE', 'LVFAILURE': 'TRUE'}, r=0.011)
print(sample)
````
With the help of the above method, a complete sample can be generated from be generated using a simple statement.

````python
sample = bn.compute_sample()
print(sample)
````
Compute Sample method relies on other methods and recursion techniques to generate a full sample.

Another method of generating a completely random sample, without any consideration of the CPDs is shown below.

````python
random_sample = bn.random_sample()
print(random_sample)
````

Finally, using all the above-mentioned methods, we are able to perform some approximate sampling-based inference
algorithms as described below.

Firstly, the Rejection Sampling Inference Algorithm.

````python
prob = bn.rejection_sample(predict={'HRSAT': 'HIGH'}, given={'PCWP': 'NORMAL', 'VENTLUNG': 'LOW'}, n=1000)
print(prob)
````
Secondly, the Likelihood Weighting Sampling Inference Algorithm

````python
prob = bn.likelihood_weighting(predict={'HRSAT': 'HIGH'}, given={'PCWP': 'NORMAL', 'VENTLUNG': 'LOW'}, n=1000)
print(prob)
````
Thirdly, the MCMC Gibbs Sampling Inference Algorithm

````python
prob = bn.gibbs_sampling(predict={'HRSAT': 'HIGH'}, given={'PCWP': 'NORMAL', 'VENTLUNG': 'LOW'}, n=10000, skip=50)
print(prob)
````
Lastly, this statement returns the likely value of the given node with its posterior probability.

````python
print(bn.predict(nodes=['HRSAT'], algorithm='gibbs', given={'PCWP': 'NORMAL', 'VENTLUNG': 'LOW'}, n=10000, skip=50))
````
