# hierarchical-bayesian-modeling-of-game-scores

How does one compare the skill across multiple people if they did different tests of different difficulties? That is the question this data set forces us to answer.

The data set is a collection of scores from daily timequessr challenges collected by Hector. The goal of the game is to guess both the location and year of a photography, and with 5 attempts á 10 000 points, the maximum possible score for each day is 50 000.

The model should consider at least two factors: the skill of each player, which determines is the crux of the matter, and the difficulty of each daily test. With that as the only requirement, I assumed the data was normally distributed. 

In particular, assume that player $i$ has skill $\theta_i$ and day $j$ has difficulty $\sigma_j$, then score is distributed according to the following distribution:

$$
y_{i,j} \sim N(\theta_i + \sigma_j, \sigma^2_y)
$$

Then, all we have do to is to estimate $\theta_i$, and the player with the highest skill is, of course, the best player.

Since we are doing this in a bayesian fashion, we need to find the posterior distribution for the skill, i.e.

$$
\theta_i | y, \sigma^2_y, \sigma_{i:n}
$$

Where $\sigma_{i:n}$ is the vector of difficulties for all days. The challenge posed by this problem is that the estimated difficulties depends on the estimated skills and vice verse. Furthermore, since there are $n=34$ days and $p=13$ players, this amounts to $47$ parameters minimum, which is considerable given that there are only $224$ samples across all participants. Therefore, a hierarchical model I used instead.

The hierarchical model assumes the following structure:

* The participants skills are samples from a single probability distribution
* The daily difficulties are samples from a single probability distribution

We must therefore simply determine these probability distributions, and Bayes' rule will dictate the rest. Again I assume a normal model for both of these since they are conjugate:

$$
\theta_i \sim N(\mu_theta, \sigma^2_\theta)
$$

$$
\sigma_j \sim N(\mu_sigma, \sigma^2_\sigma)
$$

Finally, we need a prior on all the variances $\sigma^2_y, \sigma^2_{\theta}, \sigma^2{\sigma}$. Since an inverse gamma is conjugate, I chose this with somewhat vague distribution, reflecting that I have no real idea of what the values should be.

The code then performs a simple Gibbs sampler, where it first samples all the skill parameters, then all the difficulty parameters.


