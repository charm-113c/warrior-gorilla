## The main idea

The idea is as follows: create a model that, given a series of network packets as input, predicts what the next packet would be.
More precisely, the model would be a malicious predictor. In other words, it would generate the packet it thinks likeliest to come if the series of input packets were part of an attack.
Then, if the predictions are correct for too many packets (either in a row or in close sequence) the model would trigger an alert that an attack is likely to come.

The initial plan was to leverage the research in the [BARTPredict paper](https://arxiv.org/pdf/2501.01664): it presents a framework based --as its name implies-- on the BART model among others, and BART would predict the next packet in a network.
To improve its practicality, the work would have consisted in building a lighter version of the predictor, using methods like knowledge distillation and weight pruning to make the resulting model deployable locally with minimal performance drops.
However, I soon discovered that the model's predictions were not as good as I first thought; the paper states that 73% of BART's predicted packets were *valid* network packets, in other words the percentage of good predictions must be even lower, although it's not explicited.

Thus, it is necessary to find new ways to predict packets, and in order to do that we have to clarify what we want to achieve.

## Exploring technical details

[Discuss how the model would have worked in more details, i.e. what if we did have a predictor]
To account for the context given by previous packets, the model should make use of LSTMs or transformers. It would be trained entirely on series of packets from malicious requests and would be such that, after pre-training is complete, it wouldn't need any fine-tuning on any new network.
Indeed, the model's main task is to predict malicious packets, so once trained it could be plugged-and-played anywhere. 

One aspect that needs to be studied is *when* the alert is triggered, although this shouldn't have much influence on model architecture.


## Discussing feasibility



## Alternative routes