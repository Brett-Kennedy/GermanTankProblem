# GermanTankProblem

## Background
This is a single notebook that provides some analysis and experiments related to the German Tank Problem. The problem is described in Bernoulli's Fallacy by Aubrey Clayton, (2021) Columbia Press as:

> "During World War II, in the days leading up to the invasion of Normandy, Allied forces were faced with a difficult problem in statistical inference. The German military had recently introduced a new model of tank, the Panzer V. ... The Allies needed to know how many of these tanks they could expect to encounter in northern France. ... What they had were serial numbers of various parts ... of Panzer V tanks they had either captured or destroyed, which would enable statistical analysis. Importantly, they assumed these numbers were generated in sequence, so the relative sizes of the numbers could give them some clue of how many tanks had been manufactured ... What they needed to know was the highest number that had been generated, which would reveal the total number of tanks that had been produced. They made further assumptions that ehe tank parts they discovered were equally likely to have come from anywhere in the sequence and that the parts that ended up in tanks in various places were effectively independent from one another. Relabelling the numbers from 1..., the problem became the following:

> *Given a collection of k integers, n<sub>1</sub>,...n<sub>k</sub> sampled independently and uniformly from the range from 1 to N, with N unknown, come up with a way to estimate N.* 

> It turns out that a good estimator for N is given by the statistic:

$$ \hat{N} = Max * {{k+1} \over k} -1 $$

> Where Max is the *data maximum*, meaning the largest number among n<sub>1</sub>,...n<sub>k</sub>. This captures the intuitive idea that if we had observed only one value (k=1), we'd gues it to be about midway through the list on average, so we might htink to double it as a guess for the maximum. If we had observed two values, we'd imagine them to be roughly equally spaced on average, at positions around 1/3 and 2/3 of the total. So the larger of the two should be about two-thirds of the highest possible value, suggesting a guess of about Max * (3/2), etc. 

> This estimator happens to have the friendly property of being the minimum variance unbiased estimator (MVUE) for this parameter. 

For other discussion, see Fifty Challenging Problems in Probability by Fredrick Mosteller (1965), Dover Press as the Locamotive Problem:

> (a) A railroad numbers its locamotives in order: 1, 2, ... N. One day you see a locamotive and it's number is 60. Guess how many locamotives the company has
> (b) You have looked at 5 locamotives and the largest number observed is 60. Again guess how many locomatives the company has.

This presents the same logic, at least for being as close to correct on average in the long run, using a principle called the principle of symetry: When n points are dropped at random on an interval, the lengths of the n+1 line segments have identical distribution. 

## Motivation

Considering the German Tank problem, intuitively, the largest serial number found has more signal than the serial numbers of the other parts found. But, it can be quite unititive that the best model could use only this information, ignoring the other serial numbers found, which do each provide some information. For example, relying on this single serial number allows for situations where, instead of the last number found being roughly evenly spaced, the last number is unusaully close to the second-last serial number found, leading to an underestimate of N, or the last serial number found is unusually close to the true N, leading to an overestimate of N. Using the other other serial numbers could mitigate these situations. 

This notebook explores methods incorporating information from the other serial numbers discovered.

## Experimental Setup 

We consider cases where 100 parts are found, and use a variety of estimators to estimate N, including the estimator described above, estimators including information from other parts found, and machine learning models. 

We measure accuracy as: 

$$ ABS(N - \hat{N}) \over N $$

This is averaged over 100,000 trials.

## Results
