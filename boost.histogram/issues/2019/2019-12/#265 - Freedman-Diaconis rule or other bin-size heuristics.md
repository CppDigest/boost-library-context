# #265 - Freedman-Diaconis rule or other bin-size heuristics [Open]

> Username: NAThompson  
> Created at: 2019-12-27 21:49:00 UTC  
> Updated at: 2020-01-06 00:43:44 UTC  
> Url: https://github.com/boostorg/histogram/issues/265  

Forgive me if this is somewhere in the docs; I searched around and didn't find anything.  
  
Is there a convenience API to apply the [Freedman-Diaconis rule](https://en.wikipedia.org/wiki/Freedman%E2%80%93Diaconis_rule) or some other bin-size heuristic in boost.histogram?

---

## Comment 1

> Username: HDembinski  
> Created at: 2020-01-03 16:19:10 UTC  
> Updated at: 2020-01-03 16:20:08 UTC  
> Url: https://github.com/boostorg/histogram/issues/265#issuecomment-570620281  

Hi, you were right to ask, there is something about this in the docs, but quite hidden and I didn't expect you to find it:  
https://www.boost.org/doc/libs/develop/libs/histogram/doc/html/histogram/rationale.html#histogram.rationale.comparison_to_boost_accumulators  
  
Boost.Histogram is designed for one-pass filling. To apply the Freedman-Diaconis rule or one of the many other rules, you have to do two passes over the input data. In the first pass, you compute the bin width and in the second pass, you use use the bin width to the define the axes. Since the histogram does not store the input data, only counts, you have to fill it twice. If you have to fill it twice manually anyway, then you could as well simply insert the data into some other class that computes the bin width, this would be a separate component - although we could of course make it part of the tools shipped with Boost.Histogram.  
  
There are a few problems with these rules. They only work for 1D and you cannot pick an optimal rule without knowing something about the data distribution (which could be multi-modal). You could apply the rule for each axis individually, but this would ignore correlations between pairs of input values. In short, it is currently beyond the scope of Boost.Histogram, but I am open to change my mind, if someone has a good idea on how to handle this.

---

## Comment 2

> Username: HDembinski  
> Created at: 2020-01-03 16:28:11 UTC  
> Updated at: 2020-01-03 16:28:34 UTC  
> Url: https://github.com/boostorg/histogram/issues/265#issuecomment-570622934  

Bayesian Blocks is the most promising algorithm for automatic binning known to me:  
https://www.astroml.org/examples/algorithms/plot_bayesian_blocks.html  
It suffers from less issues than the other rules, but is not perfect either. It is not a universal solution without caveats.

---

## Comment 3

> Username: NAThompson  
> Created at: 2020-01-03 17:22:49 UTC  
> Updated at: 2020-01-03 17:26:14 UTC  
> Url: https://github.com/boostorg/histogram/issues/265#issuecomment-570638582  

@HDembinski : Hugely useful discussion, thanks!  
  
I dropped the interquartile range into boost.math a couple days ago, so perhaps a two-pass recommendation for future users might be:  
  
```  
using boost::math::statistics::interquartile_range;  
double bin_width = 2*interquartile_range(v)/std::cbrt(v.size());  
```  
  
Perhaps for multiple dimensions I could add a range projection via `std::invoke` on the `value_type` of the container.  
  
In retrospect, you are correct that it feels like a separate task.

---

## Comment 4

> Username: HDembinski  
> Created at: 2020-01-06 00:43:44 UTC  
> Url: https://github.com/boostorg/histogram/issues/265#issuecomment-570966300  

That's cool, I should add this to the docs.
