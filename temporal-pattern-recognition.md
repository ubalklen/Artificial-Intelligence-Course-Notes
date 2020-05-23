# Temporal Pattern Recognition
*This whole note is under review. The following text is only a draft.*

[Machine Learning](machine-learning.md) helps us to identify patterns in static data, but it's not directly suited to deal with patterns in data that varies in time.

## Dynamic Time Warping
**Dynamic time warping (DTW)** is a technique to measure the similarity between two **time series**. A time series is sequence of data points ordered by time, e.g the values of a stock in the stock exchange during some period. We could measure the difference between two time series by simply summing up the differences between the series in each time unit, but that would not account for differences in **phase**.

DTW is a more smart approach that tries to account for the phase. Instead of summing up the differences between the points of the same time unit, we sum the differences between the minimal differences between the data points.

### Markov Processes
A **Markov process** is a statistical model that can be applied to the problem of finding patterns in temporal data. It's modeled as follows. First, we assume time is discrete. Second, we assume data at time *t* comes from a discrete random variable *X<sub>t<sub>*, and the probability of each outcome depends on the past outcomes. Then, we make the **Markov assumption**: the number of previous outcomes that influences on the probability of the current outcome is fixed positive integer *k*. If *k=1*, we have a **first-order Markov process** that can represented as a Bayesian network.

<p align="center"><img src="images\markov_process.png" title="First-order Markov process"/></p>

We assume the process is **stationary**, i.e there is a fixed *n x n* **transition matrix** *A* that holds the conditional probabilities of every random variable *X<sub>t</sub>* in the model, where *n* is the size of the random variables' domain (denoted as *S = {S<sub>1</sub>, ... , S<sub>n</sub>}*). The process is called stationary because *A* is the same regardless of the time *t*. In mathematical notation:

<p align="center"><img src="https://latex.codecogs.com/svg.latex?A=\begin{bmatrix}a_{11}&\dots&a_{1n}\\\vdots&\ddots%20&\\a_{n1}&&%20a_{nn}\end{bmatrix},a_{ij}=P(X_t=S_j|X_{t-1}=S_i)"/></p>

And since *A* holds the full conditional probability of a random variable:

<p align="center"><img src="https://latex.codecogs.com/svg.latex?\sum_{j=1}^na_{ij}=1"/></p>

>**Markovian nomenclature**
>
> A more precise term for what we call here a Markov process would be **stationary discrete-time Markov chain**. The "chain" part comes from the assumption of discrete random variables, which make possible to represent the problem as a Bayesian network.
>
> There are extensions of the Markov process that modify each one of the assumptions made here (stationarity, discrete time and discrete random variables). So a Markov process can also be defined as a broader term that encompasses all those variants. Unfortunately, the nomenclature is not standardized in the literature.


