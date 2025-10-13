Alright, let's outline what we'll need to do now that we have the idea.

As it turns out, the [second paper](https://arxiv.org/html/2408.14045v1) that uses a GPT+BERT+LSTM architecture might interest us more. Furthermore, they've also presented [this paper](https://www.sciencedirect.com/science/article/abs/pii/S0957417423025472) that's... It fits our case to a T.
Also, we might fine tune a model not to predict the full packet contents, but header field by header field.
As a quick aside, for privacy concerns and to keep data to process and output simple, we will only consider packet headers. We'll assume that's the only thing we have access to.
In fact, if we get a good predictor, we might drop the classifier model completely, be it BERT or the LSTM. If we get a good predictor, we also have a good anomaly detector.
Do we? Hm... Yes, we do get a good anomaly detector, but we don't get a good malicious packets detector. So we might have to keep the classifier. That, or defer the task to the main IDS. I see, if we could defer classification to the IDS, we could get away with having only the predictor.
Good. Now, if we are to predict the incoming packet header field by header field, what we might do is use a mixture of experts model. Every header field has limited a set of values it maps to, it can only take so many values. Obviously, some fields have a much larger value set than others: in other words, if we can create a MoE model, then we can have the experts in bigger fields be larger than that of others, e.g. the expert that predicts the IP address will be much bigger than the one who predicts whether a boolean will be true or false.
So, on top of researching the second paper as well as the new one, we need to research:
- knowledge distillation, then potentially weight pruning
- MoE
- packet header values, and ways to vectorise packets
- A way to determine whether two packets belong to the same request. This will likely require a model, and I think this is what the second BART was used for. If we can determine this, then we can easily vectorise belonging by numbering each request.

Now, taking a step back, the reason we want to employ an MoE, and why we want to predict header field by header field is that the predictor created by the researchers didn't perform that well. IIRC, only about 73% of the predicted packets were *valid* network packets. That's already a rather unsatisfactory number to begin with, but you're telling me that only that many would be valid packets in the first place? What percentage of predictions were actually correct?
It's because of that fact that I badly want to make use of the full probability set outputted by the transformers. In other words, I don't want to output a single packet and that's my prediction. I output the most likely packet**s** to come next. Since I'll output probabilities for each header field, what I'll ultimately output are cross-products of the most likely values.
Then, once we do have that set of most likely packets, we can feed it to the IDS. More exactly, we would have to "create" a new part of the IDS that specifically classifies predicted packets, while the IDS itself will keep on monitoring incoming packets as usual.
Essentially, the IDS will need to be extended to also analyse predicted packets, so that it may send alerts if an attack is predicted.

That's wrong. I need the BERT model. If I only predict an attack one packet before it happens, then we might as well not predict anything at all.
Fuck...
**It's not packets that I need to predict. What I need is, given a series of packets, know what are the chances that an attack is coming.**
That's... a completely different beast. Shit. We've already got excellent classifiers. We don't need to classify, we can leave that to others. **We need to predict attacks, not packets.**
In this case, it becomes crucial to determine whether which request a packet comes from.

Now, there's something we could do that would absolutely kill the lightweighted-ness we've been wanting: we could simply predict multiple packets in a row and feed that to the predictor IDS, see what it says.
... How many packets does an attack usually take? As many as your average request, likely. Then, many. Yup. Which makes prediction... Unfeasible.
Or rather, say an attack takes 10 packets. That's a very low number. But say it takes 10. Then, say that our model is unreasonably good and predicts the next packet with an accuracy of 90%. Then, if we were to predict the next 10 packets correctly, it would still only say that the chances of an attack is $0.9^{10}$. Whatever that number is, it's gonna be very low. And, if attacks take 100 packets, then it'll be near 0.

I fucking got it. We need a malicious predictor. We don't need to predict what the next packet will be. **We need to predict what the next packet would be if it were part of a malicious request.**
Then it's easy. If five packets in a row correspond to what a malicious request would send, then we send an alert.
What we need to do is train a model on malicious packets only. Have it predict malicious packets only. I.e.: given the current packets, what would be the next packet if the request were malicious?
Now of course the main problem is: predicting is difficult. Let's disregard model weight for a second. Let's disregard it completely, in fact. What we need to do now is predict what the next packet is. We have groundwork, but we don't have much else. We'll need to come up with some new shit.

At some point we'll need to organise these ideas properly. Even better, let's think in an organised manner, so we don't have to re-organise later.


