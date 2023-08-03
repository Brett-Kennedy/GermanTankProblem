# GermanTankProblem

## Background
This is a single, simple [notebook](https://github.com/Brett-Kennedy/GermanTankProblem/blob/main/Tank%20Parts.ipynb)  that provides some analysis and experiments related to a probability problem know as *the German Tank Problem*. The problem is described in Bernoulli's Fallacy by Aubrey Clayton, (2021) Columbia Press as:

> "During World War II, in the days leading up to the invasion of Normandy, Allied forces were faced with a difficult problem in statistical inference. The German military had recently introduced a new model of tank, the Panzer V. ... The Allies needed to know how many of these tanks they could expect to encounter in northern France. ... What they had were serial numbers of various parts ... of Panzer V tanks they had either captured or destroyed, which would enable statistical analysis. Importantly, they assumed these numbers were generated in sequence, so the relative sizes of the numbers could give them some clue of how many tanks had been manufactured ... What they needed to know was the highest number that had been generated, which would reveal the total number of tanks that had been produced. They made further assumptions that the tank parts they discovered were equally likely to have come from anywhere in the sequence and that the parts that ended up in tanks in various places were effectively independent from one another. Relabelling the numbers from 1..., the problem became the following:

> *Given a collection of k integers, n<sub>1</sub>,...n<sub>k</sub> sampled independently and uniformly from the range from 1 to N, with N unknown, come up with a way to estimate N.* 

> It turns out that a good estimator for N is given by the statistic:

$$ \hat{N} = Max * {{k+1} \over k} -1 $$

> Where Max is the *data maximum*, meaning the largest number among n<sub>1</sub>,...n<sub>k</sub>. This captures the intuitive idea that if we had observed only one value (k=1), we'd gues it to be about midway through the list on average, so we might think to double it as a guess for the maximum. If we had observed two values, we'd imagine them to be roughly equally spaced on average, at positions around 1/3 and 2/3 of the total. So the larger of the two should be about two-thirds of the highest possible value, suggesting a guess of about Max * (3/2), etc. 

> This estimator happens to have the friendly property of being the minimum variance unbiased estimator (MVUE) for this parameter. 

For other discussion, see Fifty Challenging Problems in Probability by Fredrick Mosteller (1965), Dover Press, presented there as the Locamotive Problem:

> (a) A railroad numbers its locamotives in order: 1, 2, ... N. One day you see a locamotive and it's number is 60. Guess how many locamotives the company has
> (b) You have looked at 5 locamotives and the largest number observed is 60. Again guess how many locomatives the company has.

This presents the same logic, at least for being as close as possible to correct on average in the long run, using a principle called the principle of symetry: When n points are dropped at random on the unit interval from 0 to 1, this creates n+1 line segments within the interval. For example, if 4 points are dropped, assuming none are in the identical location, 5 line segments are created, from 0.0 to the 1st, from the 1st to the 2nd, the 2nd to the 3rd, the 3rd to the 4th, and the 4th to 1.0. If we repeatedly drop 4 points, we repeatedly create a set of 5 line segments. We can, over time, get the distribution of sizes of each of the 5 segements. It turns out, the lengths of the n+1 line segments have identical distributions, hence a symetry between them. I won't provide the proof if this here, but the idea is the same with the German Tanks Problem: we assume the distribution of gaps between each discovered part number is the same, and that the gap from the max found to N is the same, on average, as any other gap, and so N can be estimated well given the maximum number found. 

## Motivation

Considering the German Tank problem, intuitively, the largest serial number found has more signal than the serial numbers of the other parts found. But, it can be unititive that the best model would use only this information, ignoring the other serial numbers found, which do each provide some information. For example, relying on this single serial number allows for situations where, instead of the last number found being roughly evenly spaced, the last number is unusually close to the second-last serial number found and far from the true N, leading to an underestimate of N, or the last serial number found is unusually close to the true N, leading to an overestimate of N. Using the other serial numbers could mitigate these situations. 

This notebook explores methods incorporating information from the other serial numbers discovered.

## Experimental Setup 

We consider cases where 100 parts are found, and use a variety of estimators to estimate N, including the estimator described above, estimators including information from other parts found, and machine learning models. 

We measure accuracy as: 

$$ ABS(N - \hat{N}) \over N $$

The experiments test using each of the discovered part numbers, using a similar formula:

$$ \hat{N} = Value Found * {{k+1} \over rank} -1 $$

For example, if using the 80th of 100 parts found, the estimate is: (value of 80th part * (101/80)) - 1.

Note: this is identical to the formula above when using the last part found, where rank = k.

Experiments then take the mean or median of the estimates. A technique referred to as *capping* is also employed, where estimates that are known to be impossible, as they are less than the observed maximum, are excluded from the pool of estimates to be averaged. 

The errors are then averaged over 100,000 trials, taking the median error.

Additionally, we test a method that calculates the maximum part found plus the average gap between the parts found. If 100 parts are found, we have 99 gaps between them, and the average size of these gaps may be assumed to be close to the gap between the maximum found and N. 

## Results Not Considering the Average Gap

In this section, we simply consider estimates based on one or multiple part numbers found. Examining first where only the maximum is used, the experimental data appears as: (with the true N in red and estimate in green):

!["max"](https://github.com/Brett-Kennedy/GermanTankProblem/blob/main/images/using_max.jpg)

Examining next using other parts, we first look using the first 10 found:

!["first 10"](https://github.com/Brett-Kennedy/GermanTankProblem/blob/main/images/using_first_10.jpg)

Then the middle 10:

!["middle 10"](https://github.com/Brett-Kennedy/GermanTankProblem/blob/main/images/using_mid_10.jpg)

Then the last 10:
!["last 10"](https://github.com/Brett-Kennedy/GermanTankProblem/blob/main/images/using_last_10.jpg)

Using all:
!["all"](https://github.com/Brett-Kennedy/GermanTankProblem/blob/main/images/using_all.jpg)

## Results Summary

It can be seen that estimates based on the first, or even middle parts found have very high variance, and even taking their mean or median results in weak estimates. The later the parts numbers used, the stronger the estimate, with using only the single last part number found performing the best. 

## Results Considering the Average Gap

This experiment was not included in the notebook, but as expected, estimating based on the last part number found plus the average gap peformed identically to using the last part. That is expected, as the formulas are actually identical. The average gap is simply the largest part number divided by the number of parts. However, using the average gap may be a more intuitive way to think about the problem: if there are, say, 100 parts found, with the largest having value 1500, and the average gap between them is 151, then it follows that the largest value is the largest found plus the average gap, in this case 1500 + 151 = 1651. 

### Experiments with Random Forest

Experiments with random forests were done using all 100 parts found as features. As the training size was increased, the random forest learned better to use less features, and to rely more heavily on the later features. Given a sufficient number of training records (approximately 4,000 to 10,000 in this case), the random forest learns to rely entirely on the maximum part found. See the notebook for more details. 

## Conclusions
This is an example of a common theme in machine learning where adding features, even features with real signal, to a model actually decreases, as opposed to increasing, the overall accuracy. In this case the features are clearly non-independent of each other, hense the lack of improvement from incorporating them. 
