
# Decision making models

For the full code and explanation see `decision-making-models.ipynb`

Running instructions:



## Reinforcement learning model

There is evidence that reinforcement learning is impaired in patients
suffering from major depressive disorder (see [Chen et al. (2015)](https://pubmed.ncbi.nlm.nih.gov/25979140/) for a review).

This section will focus on modelling behavioural impairments
during a probabilistic reversal learning task inspired by an article of [Dombrovski et al. (2010)](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3020386/).

### Experiment

The experiment was performed by 18 MDD patients and 35
healthy control participants matched for age, sex and IQ. In each trial of the experiment, participants were
asked to make a simple choice between two stimuli. Each stimulus had a certain probability of being followed by a reward (as opposed to no reward). The probabilities were unknown to participants and changed every
30 trials, switching between pairs of probabilities. The participants were unaware of these switches.

The paired probabilities were:
- 50% for stimulus *A* and 75% for stimulus *B*, and
- 75% for stimulus *A* and 25% for stimulus *B*.

The stimulus always appeared the same to participants, only the reward probability changed.

Participants were asked to maximize their reward over 240 trials.

### Model

The behaviour of participants will be modeled using a simple reinforcement learning model. The value
of the chosen stimulus *i* will be updated on trial *t* after observing reward *r*:

![V_t^i = V_t^i + \epsilon + (r_t - V_t^i)](https://render.githubusercontent.com/render/math?math=V_t%5Ei%20%3D%20V_t%5Ei%20%2B%20%5Cepsilon%20%2B%20(r_t%20-%20V_t%5Ei))

The initial *V* values are set to 0. When the outcome of one stimulus choice is observed, the value of the
alternative option is not updated. 

The probability of choosing stimulus *A* as opposed to stimulus *B* on trial
*t* is modelled using a softmax function


![$P(\text{action } A | V_t) = \frac{\mathrm{exp}(V_t^A)}{\mathrm{exp}(V_t^A) + \mathrm{exp}(V_t^B)}$](https://render.githubusercontent.com/render/math?math=%24P(%5Ctext%7Baction%20%7D%20A%20%7C%20V_t)%20%3D%20%5Cfrac%7B%5Cmathrm%7Bexp%7D(V_t%5EA)%7D%7B%5Cmathrm%7Bexp%7D(V_t%5EA)%20%2B%20%5Cmathrm%7Bexp%7D(V_t%5EB)%7D%24)

Therefore, there are two parameters:

- learning rate and
- inverse temperature. 

We hypothesise that depression could be related to changes in reward learning
(i.e. an altered learning rate) or some change in decision making (inverse temperature)

### Alternative models

Then, two more models are being considered. 

#### Model 1:

The first model gets rid of the inverse temperature parameter when calculating the probability and introduces a new parameter when calculating the reinforcement learning values: a reward sensitivity (RS) parameter.

![V_t^i = V_t^i + \epsilon + (\rho \times r_t - V_t^i)](https://render.githubusercontent.com/render/math?math=V_t%5Ei%20%3D%20V_t%5Ei%20%2B%20%5Cepsilon%20%2B%20(%5Crho%20%5Ctimes%20r_t%20-%20V_t%5Ei))

![$P(\text{action } A | V_t) = \frac{\mathrm{exp}(V_t^A)}{\mathrm{exp}(V_t^A) + \mathrm{exp}(V_t^B)}$](https://render.githubusercontent.com/render/math?math=%24P(%5Ctext%7Baction%20%7D%20A%20%7C%20V_t)%20%3D%20%5Cfrac%7B%5Cmathrm%7Bexp%7D(V_t%5EA)%7D%7B%5Cmathrm%7Bexp%7D(V_t%5EA)%20%2B%20%5Cmathrm%7Bexp%7D(V_t%5EB)%7D%24)

The model is fit to the real data using  *learning rate=0.5*  and  *reward sensitivity=5.5* for the starting parameters. 

#### Model 2:

The second model uses the same equations as model 1. However, instead of having [0,0] as the initial RL values, it is set as [0.3, 0.1].

### Model comparison and model recovery

Model comparison and model recovery is performed to determine the best-fitting model to the data.

## Drift Decision process

Decision processes can be modeled using a drife-diffusion process. When an individual is asked to make a binary choice on the basis of an available stimulus, the assumption is that evidence from the stimulus is accumulated over time and a decision is made as soon as an upper or lower boundary is reached. Which boundary is reached determines which response is given and the time required to reach it determines the response time.

To model a decision process between two hypotheses, *h+* and *h-*, we use a Wienet diffusion process *W(t)* with drift rate *v* and standard deviation *s*, simulated using

![$W(t+dt) = W(t) + v \times dt + s \times \eta$](https://render.githubusercontent.com/render/math?math=%24W(t%2Bdt)%20%3D%20W(t)%20%2B%20v%20%5Ctimes%20dt%20%2B%20s%20%5Ctimes%20%5Ceta%24)
- if *W(t) < 0*, a decision in favour of $h^-$ is made, 
- if *W(t) > a*, a decision in favour of $h^+$ is made, where $a$ is the separation of the boundaries.

*η* represents a Gaussian noise with mean 0 and variance *dt*.

When there is no bias, *W(0)=a/2*. When there is bias when there is bias towards *h+*, *W(0)* is moved linearly up (if bias *>* 0);  when there is bias towards *h-* (if bias < 0) it is moved linearly down.
