# #63 - Quadratic quantile interpolator does not guarantee continuity [Open]

> Username: adimajo  
> Created at: 2024-04-16 08:57:43 UTC  
> Updated at: 2024-04-16 08:57:43 UTC  
> Url: https://github.com/boostorg/accumulators/issues/63  

In `[weighted_][extended_]p_square.hpp`, the p-square algorithm (an online - in the sense that it doesn't require storing all samples - quantile estimator) is implemented. Additionally:  
* a weighted version (that is, incoming samples are given a weight) is provided  
* an extended version (which allows the estimation of several quantiles) is provided.  
  
The extended version also introduces the ability to use *interpolation*:  
* Suppose an accumulator set for `[weighted_]extended_p_square_quantile` is instantiated with requested quantiles `{0.001, 0.2, 0.5, 0.8, 0.999}`  
* Internally, the accumulator set (implementing the p-square algorithm) actually estimates the following quantiles: `{0, 0.0005, 0.001, ~0.1, 0.2, 0.35, 0.5, 0.65, 0.8, ~0.9, 0.999, 1}` where quantiles 0 (resp. 1) corresponds to the min (resp. max) value seen, and values not present in requested quantiles are mid-points between requested quantiles.  
* Independently from this implementation detail, the `boost::accumulators::quantile` method can be used with this accumulator set to extract a desired quantile estimate.  
* If this desired quantile corresponds to a requested quantile, it is obviously directly returned.  
* If not, then depending on the accumulator set's constructor's parameters, a linear or quadratic interpolation is provided.  
  
Unfortunately, the choice of the quadratic interpolator polynomial introduces "jumps" in the estimated quantile function.  
  
In [`extended_p_square_quantile.hpp` (currently line 154)](https://github.com/boostorg/accumulators/blob/develop/include/boost/accumulators/statistics/extended_p_square_quantile.hpp#L154),  
`if ( (dist == 1 || *iter_probs - this->probability <= this->probability - *(iter_probs - 1) ) && dist != this->probabilities.size() - 1 )` will switch to a different polynomial around the mid-points of requested quantiles (excluding first and last mid-points).  
  
This creates situations where $`\exists \; 0 < i < 1, \exists \; \eta, \; \forall \; \epsilon, \; \hat{q}(i + \epsilon) - \hat{q}(i) > \eta`$. In other words, $\hat{q}(i + \epsilon)$ will not converge to $\hat{q}(i)$ when $\epsilon$ goes to 0, and there is a discontinuity or "jump" in the quantile function.  
  
To illustrate this claim, the programs [`MWE(3_)4.{cpp,py}`](https://github.com/boostorg/accumulators/files/14994279/MWE4.zip) do the following:  
  
* Instanciate an accumulator_set of type weighted_extended_p_square_quantile with quadratic interpolation and give it quantiles to track `{0.7, 0.8, 0.95, 0.99, 0.999, 0.9999}`.  
* Do 10000 times:  
    * Draw a sample from standard normal distribution $\mathcal{N}(0, 1)$.  
    * Estimate quantiles `{0.874999, 0.875}`.  
* Plot the estimates: estimates should obviously be very close one another if the estimated quantile function is continuous. However 0.875 lies between 0.8 and 0.95 where the quadratic interpolation polynomial changes from using `{0.7, 0.8, 0.95}` to using `{0.8, 0.95, 0.99}` according to the rule linked above.  
  
![MWE4](https://github.com/boostorg/accumulators/assets/23557886/c13f87c5-f758-4e1a-bca7-b07a848563d6)  
  
Note also that this discontinuity is "on the wrong side", *i.e.* similar to issue #62, we get that $\hat{q}(0.874999) > \hat{q}(0.875)$.  
  
Since this makes no mathematical sense, I would either issue a strong warning at instantiation or deprecate this interpolator (see also issue #62). If an additional interpolator (w.r.t. the linear one) is needed, I would suggest looking into integrating splines which are continuous by design (see *e.g.* https://github.com/ttk592/spline/).  
  
Notes:  
* Program MWE4 can be compiled e.g. via: `g++ -I$BOOST_INCLUDE_PATH MWE4.cpp -o MWE4`  
* Data is generated via `MWE4 > data4.csv`  
* Plots are generated via `python3 MWE3_4.py 4`  
* `MWE3_4.py` requires `matplotlib` and `pandas`
