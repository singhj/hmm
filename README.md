hmm-py-tk
=========

See https://code.google.com/p/hmmpytk/wiki/Tutorial for an initial starting point for this project. I have copied this page over because Google Code is going away 
and there is no telling what happens to the wiki when that happens. For the remainder of this README, *I* refers to the original author of the wiki.

This is a tutorial of how to use hmmpytk.  

![](http://www.cs.cmu.edu/~yuchenz/pics/logo_small.png) 

Introduction
------------

In this tutorial, I&#x27;ll talk about how to use hmmpytk.  

Contents will be covered: 

 1.  Importing and get started
 2.  Setting the states and observations
 3.  Doing Viterbi decoding
 4.  Training using forward-backward algorithm
 5.  Manually setting the HMM parameters
 6.  Saving/loading the model

**Importing and get started** 

To use hmmpytk, you do a import like this: 

```
from hmmpytk import hmm_faster
```

To create a HMM object:  

```
hmm_model = hmm_faster.HMM()
```

The `hmm_faster.HMM()` constructor takes five optional parameters:  

```
def __init__(self, states = None, observations = None, init_matrix = None, trans_matrix = None, emit_matrix = None)
```

If you would like, you can specify the states, observations, init_matrix, trans_matrix and emit_matrix using the constructor. If you are just creating an empty HMM, the constructor with no parameters will be sufficient.  

**Setting the states and observations** 

As mentioned in the design principle, the states and observations in hmmpytk can be anything (they don't even need to be the same type, since Python list can hold elements of different types).  

To set the states, call `set_states()` method and pass in a list that contains all the **possible** states, e.g.: 

```
hmm_model.set_states(['NN', 'NP', 'VBD', 'JJ'])
```

You can also add/remove individual states: 

```
hmm_model.add_state(st)
hmm_model.remove_state(st)
```

To set the observations, call <tt>set_observations()</tt> method and pass in a list of all **possible** observations, e.g.: 
```
hmm_model.set_observations(['lamp', 'light', 'race', 'huge'])
```

Like the states, you can add/remove individual observations as well: 

```
hmm_model.add_observation(ob)
hmm_model.remove_observation(ob)
```

**Doing Viterbi decoding** 

Given a list of observations, the Viterbi algorithm will return the most probable state sequence:  

```
hmm_model.viterbi(['them', ',', 'they', 'asked', 'him', 'no', 'question', ',', 'for', 'his', 'face', 'told', 'them', 'everything', '.'])
```

The model (trained in advanced to do POS tagging) will return something like this: 

```
['PRP', ',', 'PRP', 'VBD', 'PRP', 'DT', 'NN', ',', 'IN', 'PRP$', 'NN', 'VBD', 'PRP', 'NN', '.' ]
```

**Training using forward-backward algorithm** 

Given a list of observations, the forward-backward algorithm will train the prior matrix, transition matrix, and emission matrix to give the maximum likelihood estimation on the training data. The `train()` method takes the observation sequence, as well as two optional parameters:  

 *   max_iteration: the maximum number of times to run the forward-backward algorithm
 *   delta: the algorithm stops when the likelihood on training data between two iterations is smaller than delta
```
hmm_model.train(['PRP', ',', 'PRP', 'VBD', 'PRP', 'DT', 'NN', ',', 'IN', 'PRP$', 'NN', 'VBD', 'PRP', 'NN', '.' ], max_iteration=100, delta=0.0001)
```

**Manually setting the HMM parameters** 

You can also initialize/set each of the matrices manually.  

To initialize all the matrices randomly, you can use the `randomize_matrices()` method: 

```
hmm_model.randomize_matrices(seed = 10)
```

It takes a `seed` parameter, which is feed into `random.seed()`. 

To set the initial, transition and emission matrices, you can use:

```
hmm_model.set_initial_matrix(Pi_matrix)    
hmm_model.set_trans_matrix(A_matrix)
hmm_model.set_emit_matrix(B_matrix
```

The initial matrix is a dictionary, meaning the (log) probability of starting at certain state: 

```
{'PRP$': -3.805499867927401, 'VBG': -4.573257912278521, 'VBD': -3.4242917374647965, ...} 
```

The transition matrix is a dictionary of dictionary, meaning the (log) probability of transitioning from one state to another state:

```
{'NN': 
    {'PRP$': -6.299178999872462, 'VBG': -4.889717579471037, 'VBD': -2.639631811161477, ...}, 
 'VBD': 
    {'PRP$': -3.0631572498687802, 'VBG': -3.7613169722522697, 'VBD': -6.282033074736981, ...} 
 ... 
}
```

The emission matrix is also a dictionary of dictionary, meaning the (log) probability of emitting certain observation at certain state: 

```
{'NN': 
    {'fishing': -8.155476990238087, 'rocked': -inf, 'happiness': -7.280008252884188, 'rapid': -inf, ...}, 
 'VBD': 
    {'carried': -5.687325966990288, 'Darnay': -inf, 'getting': -inf, 'universe': -inf, ...} 
 ... 
}
```

**Saving/loading the model** 

The trained HMM model can be saved using `write_to_file()` method: 

```
hmm_model.write_to_file('./model.hmm')
```

Alternatively, you can get the complete model as a tuple using `get_model()` method: 

```
(states, obs, init_matrix, trans_matrix, emit_matrix) = hmm_model.get_model()
```

To load a previously save model, use `read_from_file()` method: 

```
hmm_model.read_from_file('./model.hmm')
```

**Miscellaneous** 

Verbose mode: 

```
hmm_model.set_verbose(True)
```

Verbose mode is turned off by default.
