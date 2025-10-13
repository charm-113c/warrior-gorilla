First, we get an idea. To do that, we finish reviewing AI related concepts.
At the same time though, this feels like a waste. Perhaps it's my impatience. Perhaps there's truth in my feelings. I feel like we should worry about finding an idea first and expanding on it later.

Change of plans: we learn as we go. Our goal is now to create an IDS --is that a valid thesis idea? It may not be something the prof likes, let's think of a twist.
The professor seems to like original and practical ideas. Practical is not the right word. Actionable. He likes theory, so ours may be entirely theoretical idea with, at best, some proof-of-concept accompanying, but it needs to be meaningful, actionable.
In other words, we may sacrifice generality for a highly specific but useful thesis idea. Something that combines deep learning and cyber-security...
If we don't know our tools, we don't know what we can do. Let's review about CNNs/RNNs, and LSTMs. Those should cover the very basics. To move forward, we may need to think of transformers and how they could be applied.
または、 the GAN idea. A GAN to assist IDSs? A GAN as an additional IDS component... Feels redundant. What purpose would the GAN have, if anomaly detection is already in place? Very well then, we try to think of an idea first, and study feasibility later.
Deep learning plus offensive capabilities... To generate malware... That's... Not something I particularly want to do.
Then, deep learning plus defensive capabilities.
Offence is the best defence. We can think of attacking.
However, thinking about it, considering the latest hacking news, I want to be able to analyse packages being uploaded to things like npm. How the heck did those things make it to npm anyway?
I see. That's how. Nothing we can do against that.
We need knowledge. We need to learn. And learn a lot. Let's get learning then.

## Focus on IDSs

More precisely, on their anomaly detection part, as that would allow the application of DL. Now, as to what exactly... I'm leaning into the application of transformers, and possibly of GANs. In other words, we need more information to get a better idea of what we could do. A deep dive into IDSs it is.
No. First, we ask questions: what do you want? What do you want to focus on?
I want... I'm gonna need something original and applicable. Let's explore our idea of using transformers. Let's develop it. And to do that, I want to know how transformers are being used in IDSs, right now.
BARTPredict, NIDS-GPT. Two papers on a similar subject.
Our idea is applicable, however it must take into consideration the current limitations of both the target field and the technologies we intend to use.
So, clearly ours is an interesting idea, however I get the vibe that it's rather impractical. Transformers... Let's learn more about them. I want to see if we can figure out some way to make our idea more lightweight, more adaptable, more usable in combination with already present IDS technologies; in other words, I want to see if we can make something lightweight that can be integrated into IDSs, thus reducing potential training and operational costs.
The BARTPredict paper is particularly interesting. It would be great if we could make it into an XAI, make its decisions explainable.
Hm... Something else is bugging me. Namely, how the paper isn't mentioned anywhere else. Its lack of impact is bothering me.
Then, let's make it more lightweight. Let's take their work and make it applicable everywhere: their current model is a full-fledged anomaly detection (or rather, prediction) system. If we can make it more lightweight, it can become the predictive component of an already complete IDS, rather; an additional component rather than the entire thing.
Sounds good, how would you do that?
Well, obviously the first thing to do is study the paper; only by knowing how BARTPredict works can we figure out how to make it lighter. Furthermore, the researches who wrote the BARTPredict paper also made another one about six months before it, on a very similar project, only one that used LSTMs instead of BART. The rest of the architecture appears to be very similar too. We can use both for reference then.

This won't be a walk in the part. Already knew that though.
In short, there's a shit ton of stuff I don't know, and I'd have to learn as I go.
And yet, if we can do it... If I can do it... What won't I be able to do?
Alright, so the current thesis idea is to **replicate BARTPredict, but lighten it so as to make it into a predicting component of a functional IDS**, rather than let it be the full-fledged IDS on its own.
Before that, however, we at least ask for the green light from the professor. We'll write the draft of an abstract and send it.