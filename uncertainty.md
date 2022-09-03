# Uncertainty

>**An Introductory Note**
>
>Dealing with uncertainty has all to do with Probability. At first, I thought it would be easy. Coins have 50% of chance to fall on each site, dice have 1/6, and so on. I soon found out it's not the case. Probability is *very hard*. It doesn't help that most material throws the bunch of the theory right away, only to apply all that powerhorse to very contrived toy examples. This is somewhat frustrating. Fortunately, there is a gem called [Bayesian Methods for Hackers](https://github.com/CamDavidsonPilon/Probabilistic-Programming-and-Bayesian-Methods-for-Hackers). I highly recommend anyone with a Computer Science background struggling with probability to take a look at that material, which is free and in a Python notebook format. Not only that helped me to better understand Probability, it also made me enjoy it. In fact, I've got so interested that I'm creating a separate note repository which dives a little deeper than this one on the subject.

## Probability
In order to perform well in more complex situations, our AI agent must deal with uncertain events. To handle this, the agent designer has to introduce the concept of uncertainty in his model. The most used framework in science to deal with uncertainty is the **Probability Theory** (there are others, such as Fuzzy Logic). In Probability, a real number from 0 to 1 is attached to an event, indicating how probable that event is. If the probability of an event is 0, this means it's impossible for the event to happen. If the probability is 1, it's absolutely certain that the event will occur. For everything else we are not 100% certain, we use real numbers between 0 and 1.

The events are outputs of **random variables**. The probability that the random variable *X* has the value *x* is denoted as *Pr(X = x)*. We can also simply write *Pr(X)* to refer to the probability of all possible outcomes of a random variable.

## Distributions
A **distribution** is a function *P(X)* that assigns a probability to the possible values of a random variable. If the domain of a random variable is discrete, it's distribution is called **probability mass function** and it's the same as *Pr(X)*. An example of probability mass function is the Poisson distribution (for the domain of natural numbers):

<p align="center">
    <img src="https://latex.codecogs.com/svg.latex?P(X=k)=\frac{\lambda^ke^{-\lambda}}{k!},\;\;k=0,1,2,\dots"/>
</p>

If the domain of a random variable is continuous, it's distribution is called **probability density function** (also known as **pdf**). An example of pdf is the exponential distribution (for the domain of non-negative real numbers):

<p align="center">
    <img src="https://latex.codecogs.com/svg.latex?P(X=z)=\lambda%20e^{-\lambda%20z},\;\;z\ge0"/>
</p>

In order to account for the continuity of the domain, a pdf does not strictly maps the elements of the domain to a probability, but to a **probability density**. The actual probability is the area under the curve of a given range of the domain. This means that the probability of any single element of the domain is zero.

In both examples, the parameter λ gives the flexibility of producing different variations of the same distribution. So, strictly  speaking, they both are families of distributions.

>**The Probability of a Single Element of a Continuous Domain**
>
>You may have been confused with the fact that, in a continuous domain, the probability of an exact single element of that domains is zero. This not intuitive. If every element of a set has zero probability, how the probability of some event is not?
>
>Unfortunately, I don't have a definite answer for this. It all boils down to the way our brain interprets the infinite, which is known as being unintuitive for human beings. The answer maybe trivial to a mathematician, but it's definitely not to me and I guess to other people.
>
>Still, this incomprehension bothers me. But I've been able to find some explanations that are somewhat convincing. In the end, I elaborate myself an argument that allows me to sleep at night.
>
>The first explanation for this paradox is considering the case when the probability of any event must be equal to the others. Then, if the probability of an element is greater than 0, the probability of all other elements are the same positive number. But in this case, the total probability won't some up to 1. There are infinite elements in the set and if you sum their probabilities, even if they were small, this will result in infinite. So we say the probability of a single event is 0.
>
>But the explanation I find more intuitive is that *Pr(X = x) = 0* in a continuous domain because a continuous domain is an uncountable set and it's **physically** impossible to pick a number from an uncountable set. Grossly speaking, an uncountable set is one that you can't elaborate an algorithm to count all its elements. This is not because the set is infinite. The set of the natural numbers is infinite, but still countable. I can devise an algorithm that count all the natural numbers by going from 0 to 1, then to 2, than 3 and so on.
>
>To illustrate the physical impossibility of picking a specific number in an uncountable set, let's do a thought experiment. Imagine there is a line segment that represents all real numbers from 0 to 1. Your task is to mark a specific point on this line with a pen. Let's say the point you have to mark is 0.75. You will end up with something like this:
>
>Now imagine I have an extraterrestrial ultra-powered microscope that can zoom in infinitely. I use this device and zoom in all the way to the point you marked. Now I see your pen's mark as a big circle. I say: "Hey, you didn't mark the exact point right. You also marked some points ahead and behind 0.75. I can see 0.750001 and 0.74999 also marked. Do it again, but this time mark only the number 0.75, would you?"
>
>Then you get a new pen with a finer tip and precisely mark the 0.75. Again, I use my microscope. And again, I can adjust it to see a big circle. "I still can see 0.7500000001 and 0.74999999999. Try again".
>
>And then you got another pen, and another and another. And every time I will be able to zoom in your mark and see that, in reality, you didn't mark a single point, but a range instead.
>
>The same goes to probabilities in uncountable domains. They're zero for an exact event because you can never, ever pick an exact element from that set. All you can do is pick a range of elements and that range can have a probability greater than 0.
>
>If you are now wondering how the hell this happened, it's because real numbers aren't really real! Mathematics is a creation of humans to try to understand the world around, but it's not a perfect representation of reality.

## Independence
In a **probability model**, we usually identify more than one random variable. We say that two random variables are **independent** if the output of one variable doesn't affect the probability of the other. For example, if you throw one die and one coin, the fact that the die outputs 6 doesn't influence the output of the coin (it still has the same probability of producing heads or tails had you only thrown the coin). In mathematical notation, the random variables *X* and *Y* are said to be independent if *P(X | Y) = P(X)* and *P(Y | X) = P(Y)*, where *P(X | Y)* reads "probability of X given that Y occurred" and is called **conditional probability**.

When the random variables are independent, their **joint probability** (i.e. the probability of both of them output a specific element from their respective domains) is given by *P(X)\*P(Y)*. 

If the random variables do affect each other, they are called **dependent** and that means *P(X | Y) ≠ P(X)* or *P(Y | X) ≠ P(Y)*. For example, if the sky is cloudy (random variable *X*), the probability of raining (random variable *Y*) is different from the probability of raining had the sky been clear.

In case two random variables are dependent, the probability of both of them happening is not the product of them anymore and is dependent on the phenomena being modeled.

## Bayes' formula
*This section is under review*

## Full Joint distribution
*This section is under review. The following text is only a draft.*

Once we define all the dependencies between the random variables, we can construct a table that assigns a probability of all possible events in a model. For example, if we have a probability model with the random variables *Toothache*, *Catch* and *Cavity*, all of them binary (i.e with outputs within the domain {True, False}), we may construct the following full point distribution table:



This table is known as the **full joint distribution** of the model and has all the information necessary in a probability model—all random variables, their outcomes and joint probabilities.  The probability of every single event is represented by a numeric cell. We can also find the probability of a any single outcome by marginalization or conditioning. Finally, it's possible to find the conditional probabilities.

Because models can have many random variables, each one with many (possible infinite) outcomes, the construction of this table is infeasible in real-world settings.

## Bayesian Networks
*This section is under review. The following text is only a draft.*

A **Bayesian network** is a more compact representation of a full joint distribution. It has this name because it is usually employed with the Bayesian interpretation of probability in mind. We will soon explore further this topic. For now, you may ignore any specifics attached to the "Bayesian" term.

A Bayesian network is a **directed acyclic graph**, where each vertex is a random variable and each edge represents a conditional dependence between two random variables.

## Bayesian Inference
There are some philosophical interpretations of what the probability numbers really mean. One interpretation is the **Bayesian Probability**, which held that probability measures the **belief** someone has on how likely an event is to occur given the facts known to them at the time. In this view, the probability is in one's mind, therefore it's a *subjective* measure.

Under Bayesian Probability, we may estimate the values of the parameters of a model looking at the data generated by that model. This procedure is called **Bayesian Inference** and works as follows:
1. We define proxy distributions that we hope best approximate the true distribution of the random variables of the model and attach some parameters on those distributions.
2. We consider the parameters themselves as random variables as well with a probability distribution. Those are called **prior distributions**. They represent our previous beliefs on the probabilities of the parameters.
2. Then, we calibrate the prior distributions with data we have, generating **posterior distributions**.

The distribution calibration is done with the Bayes' formula in the form of  *P(parameters | data)*. The calculation of the exact posterior distribution involves complex integrals that can't be solved by a computer even for simple models. Fortunately, there are numerical methods that make Bayesian inference feasible in many real-world scenarios. The most famous family of such methods is called **Markov Chain Monte Carlo (MCMC)**. The idea behind MCMC is to navigate on the possible outputs of a model and sample the posterior distribution with the right probability.

>**Bayesianism vs. Frequentism in science**
>
>Bayesian inference elegantly reflects what's actually being done in scientific research. A scientist examines some data and has to figure out what's really happening behind the scenes. It also allows one to incorporate previous knowledge about the phenomena being studied by defining proper prior distributions (and if we don't know anything, there are ways to build **uninformative priors**). That's why some people advocate for the use of Bayesian Probability in science.
>
>To put things into perspective, let's examine Bayesian inference through the lens of **Frequentism**, the other prevalent interpretation of probability. According to Frequentism, the probability of an event is the frequency of the occurrence of that event in the long run. The probability of a tossed coin to land heads up is 50% because that's the relative number of heads landing up after many tosses. It doesn't depend on what someone believes the probability is, therefore it's an *objective* measure. So a frequentist can't apply a scheme such as Bayesian inference, because seeing a particular dataset can't change the "true" values of the model's parameters. They are fixed, not subject to the data someone's just happened to see.
>
>Frequentists would rather build a model (with fixed parameters) and check how probable it is for that model to generate the data at hand. This is useful in science because if one constructs a model where some hypothesis is not significant and the observed data is highly improbable to have come from that model, that means the hypothesis is significant with high confidence.
