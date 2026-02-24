# #114 - Explain when to use Poisson and when to use multinomial errors [Open]

> Username: HDembinski  
> Created at: 2018-10-01 12:03:23 UTC  
> Updated at: 2019-10-19 18:11:39 UTC  
> Url: https://github.com/boostorg/histogram/issues/114  

> On 26. Sep 2018, at 15:16, a.hagen-zanker--- via Boost <boost@lists.boost.org> wrote:  
  
>Sorry to get back to the issue of variance. I am unsure about the justification of choosing the variance based on the Poisson distribution instead of the binomial distribution.   
  
> My understanding is that the Poisson distribution is based on a distribution of a number of event given a continuous domain of opportunities (say a period of time). Whereas a binomial distribution is for a number of event for a discrete number of opportunities (say coin flips).  
  
> Both seem appropriate in some use cases. However, the histogram class has no sense of the passage of time, whereas it does know the number of discrete opportunities (every time operator () is called).  And the typical use of histogram seems to be to distribute a given number of samples over the bins that they belong to.   
  
> So, would it not be more appropriate to estimate variance based on the binomial distribution?  
  
the choice is between Poisson distribution and the multinomial distribution, and it is a bit subtle.  
https://en.wikipedia.org/wiki/Multinomial_distribution  
Either can be correct, depending on the scenario.   
  
Poisson is correct, for example, when you monitor a random process for a while which produces some value x at random points in time with a constant rate. You bin the outcomes, and then stop monitoring at an arbitrary point in time. This is the right way to model many physics experiments. It is also correct if you make a survey with a random number of participants, i.e. when you pass the survey to a large number of people without knowing beforehand how many are going to respond.  
  
Multinomial is correct, when there is a predefined fixed number of events, each with a random exclusive outcome, and you bin those outcomes. The important point is that the number of events is fixed before the experiment is conducted. This is the main difference to the previous case, where the total of events is not known beforehand. This would be correct model, if you make a survey with a fixed number of participants which you invite explicitly and don't start the analysis before all have return the survey.  
  
If you have many bins in your histogram, the difference between the two becomes negligible. The variance for a multinomial count is n p (1 - p) where p is the probability to fall into this bin. The variance for a Poissonian count is p n, if you write it in the same way. If you have many bins, then p << 1 and n p (1 - p) = n p + O(p^2).

---

## Comment 1

> Username: veprbl  
> Created at: 2018-10-01 15:11:12 UTC  
> Url: https://github.com/boostorg/histogram/issues/114#issuecomment-425944051  

I think the problem lies with the naming of the ```variance()```. There is no way for the histogram to know the underlying statistics of the "fill" calls given to the histogram by the user. Even \_if\_ we assume that the events are independent, there are still many possible statistics that can be out there. In my opinion, the solution for this issue is to rename ```variance()``` to ```poisson_variance()```. This should help to encourage users to read the documentation and think about their specific use case. To answer the original question, the reason to have ```poisson_variance()``` is because it is easiest to implement estimators for. When we sum two histograms we rely on a fact that the sum of two Poisson-distributed variable is a Poisson-distributed variable, so that the information can be stored in the same kind of storage. This is not true for Multinomial distribution.

---

## Comment 2

> Username: jpivarski  
> Created at: 2018-10-01 15:16:01 UTC  
> Url: https://github.com/boostorg/histogram/issues/114#issuecomment-425945857  

`variance` was originally an attempt to avoid the technical name `sumw2`: sum of weights squared, which is literally true but far from a data analyst's interest. Would `poisson_variance()` be a correct name if the fills are weighted? (Which is the only case in which you care because it's the only case in which `sumw2` is not equal to the counts.)  
  
For the record, I thought that the switch from `sumw2` to `variance` was great because of the focus on statistical meaning, though of course the library doesn't know the statistical model without assumptions.

---

## Comment 3

> Username: veprbl  
> Created at: 2018-10-01 15:29:44 UTC  
> Url: https://github.com/boostorg/histogram/issues/114#issuecomment-425951046  

> Would `poisson_variance()` be a correct name if the fills are weighted?  
  
I think yes, because the ```poisson_``` refers to statistics. The variance is still calculated correctly, so it is a ```poisson_variance()```.  
  
@jpivarski, have you seen YODA histogramming package? They have ```sumW2()```, variance along axes and they even have method that takes square root for you. I like that package because it teaches users how to handle their data properly, unlike ROOT where you can divide and subtract histograms and then still store them as histograms.

---

## Comment 4

> Username: jpivarski  
> Created at: 2018-10-01 17:09:56 UTC  
> Url: https://github.com/boostorg/histogram/issues/114#issuecomment-425986863  

@veprbl Yes, I've tried out the YODA package. They got their `sumW2` naming convention from ROOT (and HBOOK before that). While this is technically accurate, I had been hoping we could go to more statistician-meaningful names, but perhaps actually calling it "variance" is putting in too many statistical assumptions.

---

## Comment 5

> Username: HDembinski  
> Created at: 2018-10-02 10:24:32 UTC  
> Url: https://github.com/boostorg/histogram/issues/114#issuecomment-426224315  

We seem to converge on `poisson_variance()`. I like that. It reads well and still conveys the idea that the variance could be computed in a different way.  
  
By the way, the `poisson_variance()` should  be called `bootstrapped_poisson_variance()`, because it is a variance estimate obtained by replacing the true unknown expectation value of the counts with the actually realized number of counts.

---

## Comment 6

> Username: HDembinski  
> Created at: 2018-10-02 10:26:39 UTC  
> Updated at: 2018-10-02 10:27:01 UTC  
> Url: https://github.com/boostorg/histogram/issues/114#issuecomment-426224885  

>  I like that package because it teaches users how to handle their data properly, unlike ROOT where you can divide and subtract histograms and then still store them as histograms.  
  
I second this. That's why you cannot subtract histograms in this library, only add them. Subtracting two histograms does not produce another histogram.

---

## Comment 7

> Username: HDembinski  
> Created at: 2018-10-02 10:47:06 UTC  
> Url: https://github.com/boostorg/histogram/issues/114#issuecomment-426229607  

degski on boost mailing list:  
> From what you are saying, and I have no knowledge at all in this matter [just reading what you say], it seems that a policy approach, to allow for both distributions, seems appropriate. Don't want to give you more work, but you just made the [that] point yourself.

---

## Comment 8

> Username: degski  
> Created at: 2018-10-02 10:54:54 UTC  
> Url: https://github.com/boostorg/histogram/issues/114#issuecomment-426231444  

@HDembinski The only thing I was stating is that you seem to say there is some merit in using a Binomial Distribution in certain use-cases. This, therefor might warrant a policy (which can still be defaulted to a PD). Are there any other distributions [you can think of] that could have a valid use-case?

---

## Comment 9

> Username: HDembinski  
> Created at: 2018-10-02 11:49:15 UTC  
> Url: https://github.com/boostorg/histogram/issues/114#issuecomment-426244281  

@degski Strictly speaking, there are infinitely many ways to compute variance, because it depends on the details of the process that generated the data for the histogram. Strictly speaking, we should not compute variance estimates from histogram counts at all, because the variance is a property of the underlying random process, it cannot be obtained from the data. The best we can do is to provide variance *estimates* for simple common cases, which are approximately right, but should not be used for serious analysis anyway.  
  
The only two simple cases are the Poisson-variance and the Multinomial-variance.

---

## Comment 10

> Username: HDembinski  
> Created at: 2018-10-02 14:57:29 UTC  
> Url: https://github.com/boostorg/histogram/issues/114#issuecomment-426306229  

The Poisson variance has the advantage that it can be computed individually for each point. The multinomial variance can only be computed by looking at all bins, since you must compute `n` and  `p` in `n* p *(1- p)`. This requires a global calculation.  
  
So if we want anything other than the simple Poisson variance estimate, then we need a different facility than just a `variance()` method on the returned counter type.

---

## Comment 11

> Username: ahhz  
> Created at: 2018-10-03 12:22:27 UTC  
> Url: https://github.com/boostorg/histogram/issues/114#issuecomment-426617444  

@HDembinski If you have many bins, then p << 1 and n p (1 - p) = n p + O(p^2).  
  
This is often true, but not always. Even if you have many bins, one can be prevalent.   
  
@HDembinski The multinomial variance can only be computed by looking at all bins  
  
I see your point. So, calling it ```poisson_variance()``` and not implementing ```multinomial variance()``` seems sensible.   
  
On the other hand, since variance cannot generally be considered a function of a single bin, you might prefer to make it a function of the histogram instead: ```h.poisson_variance(i)```, this would leave room to still implement ```h.multinomial_variance(i)``` later in a consistent manner.

---

## Comment 12

> Username: ahhz  
> Created at: 2018-10-03 12:36:27 UTC  
> Url: https://github.com/boostorg/histogram/issues/114#issuecomment-426621422  

>   
>   
> > I like that package because it teaches users how to handle their data properly, unlike ROOT where you can divide and subtract histograms and then still store them as histograms.  
>   
> I second this. That's why you cannot subtract histograms in this library, only add them. Subtracting two histograms does not produce another histogram.  
  
I think it can, but only if you agree on preconditions: you can only subtract what has been added before. Perhaps retracting is a better term than subtracting.  See my comment to issue #99

---

## Comment 13

> Username: jpivarski  
> Created at: 2018-10-03 12:51:29 UTC  
> Url: https://github.com/boostorg/histogram/issues/114#issuecomment-426625848  

I'm generally in favor of using histogram tools as histograms and other tools for other purposes (histograms are wildly abused: some users grab a histogram when they really want an _array_), but not allowing subtractions sounds a little prescriptive in an environment that allows negative weights. In this environment, "subtract" is just shorthand for "use negative weights and add" or "multiply by a negative scalar and add" (can you multiply by negative scalars?).  
  
Maybe if the histogram was declared without weights, or if it's in a mode where only positive weights are allowed (which would enable [stable weighted mean and variance](http://people.ds.cam.ac.uk/fanf2/hermes/doc/antiforgery/stats.pdf) for profile plots), then excluding subtraction would ensure that the bin contents never go negative (an invariant needed for that stable weighted mean and variance). However, the feedback I got from Histogrammar was that negative weights are absolutely essential for pseudodata from some Monte Carlo generators. Then if you've got 'em, you've automatically got subtraction whether you like it or not.

---

## Comment 14

> Username: veprbl  
> Created at: 2018-10-03 15:19:52 UTC  
> Url: https://github.com/boostorg/histogram/issues/114#issuecomment-426677162  

> On the other hand, since variance cannot generally be considered a function of a single bin, you might prefer to make it a function of the histogram instead: `h.poisson_variance(i)`, this would leave room to still implement `h.multinomial_variance(i)` later in a consistent manner.  
  
What would be the value of such consistency?

---

## Comment 15

> Username: ahhz  
> Created at: 2018-10-03 15:38:45 UTC  
> Url: https://github.com/boostorg/histogram/issues/114#issuecomment-426684507  

> What would be the value of such consistency?  
  
Easing the learning curve of working with the library

---

## Comment 16

> Username: HDembinski  
> Created at: 2018-10-03 20:36:37 UTC  
> Url: https://github.com/boostorg/histogram/issues/114#issuecomment-426792491  

> since variance cannot generally be considered a function of a single bin, you might prefer to make it a function of the histogram instead: h.poisson_variance(i), this would leave room to still implement h.multinomial_variance(i) later in a consistent manner.  
  
This specific proposal does not work, for several reasons:  
- `multinomial_variance(i)` would need to cache the global variable `n`, the total number of all bin counts; caching is bad  
- It violates the orthogonal design of the library: the methods are only valid if the bin counts are integral numbers, but the library shall also support sum of weights, sum of samples (profiles), and perhaps even arbitrary Boost.Accumulators.  
  
Instead, there should be a `poisson_variance_view` and `multinomial_variance_view` in the support library. These structs hold a pointer to the histogram and compute variances. Global variables like `n` are computed once at the time of construction.

---

## Comment 17

> Username: ahhz  
> Created at: 2018-10-03 22:20:29 UTC  
> Url: https://github.com/boostorg/histogram/issues/114#issuecomment-426823097  

Sounds good, how would it work? Something like below?  
  
```  
auto view =multinomial_variance_view(h);  
auto stat = view.multinomial_variance(i);  
```

---

## Comment 18

> Username: HDembinski  
> Created at: 2018-10-04 16:31:41 UTC  
> Url: https://github.com/boostorg/histogram/issues/114#issuecomment-427084906  

Yes, but as a functor.  
```  
auto view = multinomial_variance_view(h);  
auto stat = view(i, j, k);  
```
