## The idea

### The core idea

It is as follows: create a model that, given a series of network packets as input, predicts what the next packet would be.
More precisely, the model would be a malicious predictor. In other words, it would generate the packet it thinks likeliest to come if the series of input packets were part of an attack.
Then, if the predictions are correct for too many packets (either in a row or in close sequence) the model would trigger an alert that an attack is likely to come.
Such a model would be lightweight and be used alongside intrusion detection systems (IDS) as a way to enhance detection capabilities.

### Early setback

The initial plan was to leverage the research in the [BARTPredict paper](https://arxiv.org/pdf/2501.01664): it presents a framework based --as its name implies-- on the BART model among others, and BART would predict the next packet in a network.
To improve its practicality, the work would have consisted in building a lighter version of the predictor, using methods like knowledge distillation and weight pruning to make the resulting model deployable locally with minimal performance drops.
However, I soon discovered that the model's predictions were not as good as I first thought; the paper states that 73% of BART's predicted packets were *valid* network packets, in other words the percentage of good predictions must be even lower, although it's not explicited.

Thus, it is necessary to find new ways to predict packets.

## Quick recap on packets

![[Pasted image 20251015214218.png]]
Figure 1: Data packet (or actually, frame) layering following the OSI model

Whether we follow the OSI or the TCP/IP layering model, packets (or technically, frames) will be decomposed in a series of layers, the only difference between the two models being that the TCP/IP one aggregates layers 5, 6 and 7 into a single application layer.
Thus, we have the following type of encapsulation:
![[network-packet-768x518.jpg]]
Figure 2: Encapsulation of a packet


## Considerations


The following considerations need to be made with this idea:
- The plan is to make something that works alongside an IDS, and therefore it should have access to what IDSs have access to. This means:
	- Limited computational power (the model should preferably be pre-trained and need little fine-tuning). N.B.: see (1)
	- Access to the same packets, and since HTTPS is the predominant protocol, we may we'll only have access to the headers for most packets and the contents will be encrypted
- Packets have a complex structure, making it *very* difficult to actually predict them:
	- Packets have multiple layers (following either the OSI or the TCP/IP model), and headers for each layer has their own set of fields
	- Those fields (generally) cannot take an arbitrary value, but even so their value set can be very large
- We want to generate valid packets, but validity is context-dependant:
	- For example, packets cannot come from a never-before seen IP address, unless the packet they're in are part of a handshake message
	- Generalising this point, *generated packets must be consistent with the current context*. For instance, if a message spans multiple packets, the content of each packet must logically follow that of those before it, which isn't trivial to replicate
- Combining the above two points: different packets may use different protocols (e.g. TCP vs UDP in the transport layer, or HTTP vs HTTPS vs SMTP at app layer) and each packet's fields must comply with their own protocol to be valid, which is once again not easy to replicate

![[tcp_ip_packet_structure_diagram_mhic.jpg]]
Figure 3: The format of a TCP/IPv4 packet showcasing the many fields for layers 3 and 4 alone

In summary, due to those many technical factors, it is clear generating a packet is not an easy thing to do.
Thus, if we are to undertake this task, we must do so smartly.


(1) if the model cares only about predicting malicious packets, then it is possible it won't need to be fine-tuned to a particular network and be deployable anywhere. That would be because it isn't trying to study a particular network's patterns, but instead it assumes packets are sent with malicious intent. In other words this model works under the assumption that attacks follow similar patterns irrespective of where they happen. Of course, fine-tuning could still be done if we know some network is more prone to a certain type of attack than others.

### Determinism and restrictions

First of all, since HTTPS is the most used protocol, it is fair to assume that most packets will have their payload encrypted, and for those IDSs only get to analyse the upper layers.
So, rather than try to predict a full packet's contents --including the encrypted data-- *we could focus on predicting its header values*.
However, even with this reduction this task won't be easy, as the prediction space is still extremely large.

While it is true that packets' header fields can take a very large range of values, those values are not random, and in many cases they depend on one another.
We can make use of this to simplify our task. Additionally, to keep this project within the scope of a bachelor thesis, it would be wise to focus on the main protocols being used throughout the internet.
In short, to guarantee the feasibility of this thesis, it is best that we impose restrictions on the packets we want to predict.
For instance, we could limit ourselves to only the most prominent protocols, like TCP and HTTPS, which would narrow down, for example, the destination ports.
Furthermore, since MAC and IP addresses have a very large range, we could rely solely on previously-seen addresses instead of generating them from scratch.
Then, fields like checksums and total lengths can theoretically be mathematically derived from other data.
In short, **we could focus the generation on the most important header fields**, rather than 

Overall, a thorough study would need to be conducted on the restrictions and dependencies that could be leveraged.

### Potential architecture

By applying the restrictions above, we could make the model focus on predicting certain features and force it to follow (or simply fill out) a given structure.
Since IDSs are really only guaranteed access to the headers of layers 2, 3 and 4 we could a mixture-of-experts architecture or something similar, where we have experts focusing on the different layers.
Alternatively, packet generation could mirror how packets are formed theoretically, and we could "build" a packet layer-by-layer, e.g.: a first NN takes care of generating layer 2 header values, leaving fields like checksum or length for a post-processing function, and then pass the output on to a next NN that works on layer 3, and so on.

More concretely, hybrid solutions like CNN+LSTM or transformers are very common for IDSs, to capture both spatial and temporal patterns, and we could think of using something similar.

## Further considerations

Putting aside the potential difficulties, there are still many questions of technical nature that would need to be answered. Those include, but are not limited to:
How do we determine that a prediction is correct? And how many correct predictions before we sound the alarm? 
How do we deal with the massive class imbalance between benign and malicious packets?
How do we obtain the data we want to train the model in the first place? What kind of pre-processing would we need for the packets? Will they be vectorised/encoded, kept as is, or something else?

## A potential alternative

Taking all this into account, it's clear that to keep this idea within the scope of a bachelor thesis, a lot of careful thought and consideration is necessary.
What's more, it's obvious that posing restrictions will have an impact on the reach and usefulness of this thesis, and it would really serve as a building block for future research.
Even with these restrictions, this project could still prove challenging to realise.
With those things in mind, I have begun exploring an alternative idea that may have broader applications. It is as follows:
*Instead of predicting a packet, it would be simpler to predict an attack.*
More precisely, we could instead predict the chances that a current stream of packets is an attack, and update the prediction with every new packet that comes.
This is much closer to what current anomaly-based IDSs are doing, and admittedly it lacks the originality of the previous idea.
In exchange, it is simpler and has a broader range of application.