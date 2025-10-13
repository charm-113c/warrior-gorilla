This file presents a paradigm shift: instead of predicting a malicious packet, which could prove very difficult, we could instead predict the chances that a current stream of packets is an attack.
In other words, we would train a network on serious of packets from malicious and benign requests to identify...
That's just how normal DL-based IDSs work, isn't it?
Well, yes and no. The end result is what we're interested in, and ours will be a probability. What we're really thinking of is a classifier in the most standard sense, just that ours won't be giving out clean results.
Is that really any different from other anomaly-based NIDSs though?
Let's expand on what the model would do: it is placed at the edge of a network, presumably right behind a firewall alongside an IDS.
Then, unlike IDSs which may sample the packets coming in (or out), our model would sift through *every* packet that comes in (question: is that computationally feasible?).
It would keep of the different requests and the packets that belong to it (or, should that be impossible, it would at least keep track of which session each packet belongs to). Actually, it would be easier and better for our purposes if we kept track of sessions rather than individual requests. **TODO: figure out how to deal with attacks like DDoS in this case**..
Then, for each session, it would start with a pre-trained probability that the current session will result in an attack, and with every new packet exchanged it would update that probability.
The model could be fine-tuned to determine the probability of the session being benign or resulting in one of many specific attacks to increase its effectiveness, something like:
- Benign: $90\%$
- DDoS: $\lt 1\%$
- DoS: $\lt 1\%$
- Spoofing: $9\%$
- Other: $\lt 1\%$
For each session.

What we could do is have a model that focuses on each session and another that looks at the whole picture. Whether they'd be independent is something worth exploring later. Alternatively, we could drop the per-session model and just try to encode sessions into the packets' vector encodings. We could potentially lose the exact source of a potential attack, but it would make the model simpler.