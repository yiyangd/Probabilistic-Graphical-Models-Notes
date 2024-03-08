## Intro to BayesNets
### 1. Factor Graph

Definition:
- Variables $X = (X_1, ... , X_n)$, where $X_i \in \ Domain_i$
- Factors $f_1, ..., f_m$, with each $f_j(X)\geq 0$

$$\textbf{Weight}(x)=\prod^m_{j=1}f_j(x)$$

![](https://files.mdnice.com/user/1474/f0cce2ec-0106-4863-aaa6-bcddfcf816f9.png)



Factor graphs consist a set of variables. 
- These variables could denote objects at different time steps. 

Factor graphs also include a set of factors which depend on certain sets of variables, 
- and these factors are meant to specify preferences or constraints on what values are good for these variables to take on.

The weight of each assignment x is the product of all of the factors 
- The stated objective was to find the maximum weight assignment.
- Given any factor graph, a number of algorithms (backtracking search, beam search, Gibbs sampling, variable elimination) can be used to (approximately) optimize this objective.
#### Object(Hand) Tracking Example:

> Sensors report positions: 0, 2, 2. Objects(Hands) don't move very fast and sensors are a bit noisy. What path did the Hand take?

Definition:
- Variables $X_i$ : location of object(hand) at time $i$
- Transition factors $t_i(x_i, x_{i+1})$ : incorporate physics
- Observation factors $o_i(x_i)$ : incorporate sensors

![](https://files.mdnice.com/user/1474/fd057542-3cb3-405f-8961-2fd316d469e5.png)

Explain:
- we have a set of variables corresponding to the location of an unobserved object at time step i. 

- we defined observation factors to capture the fact that the true object position is close to the sensor reading, and the transition factors to capture the fact that the true object positions across  two successive time steps are close to each other.



Demo:


![](https://files.mdnice.com/user/1474/183c7132-281a-4b08-a777-1ef23008b5ac.png)

- Define the Factor Graph and all the factors which are represented in tables. 
- multiply everything together, for every joint assignment to all the variables some number that corresponds to how good that  assignment was.

Question:

what do these factors mean and how do we come up with them? How do we interpret the factors?
- We just set them rather arbitrarily. Is there a more principled way to think about these factors beyond being non-negative functions?
- Bayesian Networks give more meaning to factors
  - Bayesian networks are factor graphs plus probability.
  
### 2. Bayesian Networks
Bayesian networks were popularized in AI by Judea Pearl in the 1980s, who showed that having a coherent probabilistic framework is important for reasoning under uncertainty.



#### Definition of Bayesian Network
Let $$\color{blue}X=(X_1,...X_n)$$ be random variables.
A Bayesian network is a directed acyclic graph (DAG) that specifies a **joint distribution over X** as a *product of local conditional distributions*, one for each node:

$\color{blue}P(X_1=x_1,...,X_n=x_n) \\= \prod^n_{i=1}\color{red}{p(x_i|x_{\text{Parents}(i)})}$

Explain:
- A Bayesian network defines a large joint distribution in a modular way, one variable at a time.
- First, the graph structure captures what other variables a given variable depends on.
- Second, we specify a local conditional distribution for variable $X_i$ , which is a function that specifies **a distribution over  $X_i$ given an assignment $x_{\text{Parents}(i)}$** to its parents in the graph (possibly no parents). 
  - The joint distribution is simply defined to be the product of all of the local conditional distributions together.
#### Properties: locally normalized
All factors (local conditional distributions) satisfy:

$$\sum_{x_i}p(x_i|x_{\text{Parents}(i)})=1 \text{ for each } x_{\text{Parents}(i)}$$

- The key property is that all the local conditional distributions, being distributions, sum to 1 over the first argument.
- This simple property results in two important properties of Bayesian networks that are not present in general factor graphs.
  * Consistency of sub-Bayesian networks
  * Consistency of conditional distributions


### 3. Hidden Markov Model (HMM)
**Markov models are limiting** because they *do not have a way of talking about noisy evidence (sensor readings)*. 
- They can be extended quite easily to hidden Markov models, which introduce a **parallel sequence of observation variables**.
#### Definition
For each time step
$$t=1,...,T$$
- Generate object location
$$H_t \sim p(H_t|H_{t-1})$$

- Generate sensor reading
$$E_t \sim p(E_t|H_{t})$$


![](https://files.mdnice.com/user/1474/4192e35e-feea-4ea9-9bd7-6fb0a9f05468.png)



#### Inference: Given sensor readings, where is the object?

- In Object (Hand) Tracking, $H_t$ denotes the true object location, and $E_t$ denotes the noisy sensor reading, which might be 
- (i) the location $H_t$ plus noise, or 
- (ii) the distance $H_t$ from plus noise, depending on the type of sensor.
#### Example

![](https://files.mdnice.com/user/1474/525afe5b-9c4d-4365-821e-d6141083b46c.png)


### 4. Factorial HMM
#### Definition
For each time step
$$t=1,...,T$$
- For each object  $o \in \{a,b\}$
- Generate object location
$$H_t^o \sim p(H_t^o|H^o_{t-1})$$

- Generate sensor reading
$$E_t \sim p(E_t|H_t^a, H_t^b)$$

![](https://files.mdnice.com/user/1474/85eab2bd-e926-492a-946f-3909cead798d.png)

- An extension of an HMM, called a factorial HMM, can be used to track multiple objects. We assume that each object moves independently according to a Markov model, but that we get one sensor reading which is some noisy aggregated function of the true positions.

- For example, $E_t$ could be the set $\{H_t^a, H_t^b\}$ , which reveals where the objects are, but doesn't say which object is responsible for which element in the set.

### 4. Examples and Exercises
#### Example of a Bayesian Network
Earthquakes(E) and burglaries(B) are independent events that will cause an alarm to go off. Suppose you hear an alarm(A). How does hearing on the radio that there's an earthquake change your beliefs about burglary?
- Situations like these arise all the time in practice: we have a lot of unknowns which are all dependent on one another. 
- If we obtain evidence on some of these unknowns, how does that affect our belief about the other unknowns? This is called reasoning under uncertainty.
  - Using Bayesian networks to perform this type of reasoning under uncertainty in a principled way


![](https://files.mdnice.com/user/1474/9cd20c47-f7be-4750-9057-a0dbfc7b6a3f.png)

Step 1: Define three variables, B(burglary), E(earthquake), and A(alarm).

Step 2: Connect up the variables to model the dependencies. 
- Unlike in factor graphs, these dependencies are represented as directed edges. 
- We can intuitively think about the directionality as suggesting causality

Step 3: For each variable, Specify a local conditional distribution (a factor) of that variable given its parent variables. 
- In this example, Band E have no parents while A has two parents, B and E. 
  - This local conditional distribution is what governs how a variable is generated.

Step 4: Define the joint distribution over all the random variables as the product of all the local conditional distributions.
- Note that we write the local conditional distributions using , while is reserved for the joint distribution over all random variables, which is defined as the product.


![single factor that connects all parents](https://files.mdnice.com/user/1474/a219331a-491c-4f30-ac13-42e18be8a8c3.png)

- Note that the local conditional distributions (e.g. $p(a|b,e)$ ) are non-negative so they can be thought of simply as factors of a factor graph. The joint probability of an assignment is then the weight of that assignment.
  - In this light, Bayesian networks are just a type of factor graph, but with additional structure and interpretation.
  

![](https://files.mdnice.com/user/1474/4adf5a3c-278e-4ed6-8a58-a6c7ae13d48d.png)

- Consider the following model: Suppose the probability of an earthquake is $\epsilon$ and the probability of a burglary is $\epsilon$ and both are independent. Suppose that the alarm always goes off if either an earthquake or a burglary occurs.
- In the prior, we can eliminate A and E and get that the probability of the burglary is $\epsilon$
- Now suppose we hear the alarm A=1. The probability of burglary is now 

$$P(B=1|A=1)=\frac{1}{2-\epsilon}$$
- Now suppose that you hear on the radio that there was an earthquake (E=1), then the probability of burglary goes down to
  $$P(B=1|A=1,E=1)=\epsilon$$ 
  again

Key idea: explaining away
- Suppose two causes positively influence an effect. Conditioned on the effect, conditioning on one cause reduces the probability of the other cause.


### Markov networks versus Bayesian networks

In Markov networks, we use the factor graph to define a joint probability distribution over assignments and compute marginal probabilities.

Bayesian networks define a probability distribution using a factor graph, but the factors have special meaning.

Bayesian networks were developed by Judea Pearl in the 1980s, and have evolved into the more general notion of generative modeling.

#### Compare and Contrast

![](https://files.mdnice.com/user/1474/b1e66fdf-65ea-4517-be47-f5bd1ea7e8c0.png)

- Both define a joint probability distribution over assignments, and in the end, both are backed by factor graphs.
- 


#### Resources:
[1] https://stanford-cs221.github.io/winter2021-extra/modules/bayesian-networks/bayes-net-definition-and-inference.pdf
