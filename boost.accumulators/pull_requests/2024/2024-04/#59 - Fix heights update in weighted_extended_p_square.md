# #59 Fix heights update in weighted_extended_p_square [Open]

> Username: adimajo  
> Created at: 2024-04-15 15:52:45 UTC  
> Updated at: 2024-06-27 13:19:57 UTC  
> Url: https://github.com/boostorg/accumulators/pull/59  

In `weighted_extended_p_square.hpp`, a weighted version (that is, incoming samples are given a weight) of the extended (which allows the estimation of *several* quantiles) p-square algorithm (an online - in the sense that it doesn't require storing all samples - quantile estimator) is implemented.  
  
This algorithm works by updating estimates of these quantiles and additional "markers" (min, max values and all mid-points, i.e. all quantiles lying between two requested quantiles).  
  
Unfortunately, the heights (*i.e.* quantile estimates) update rule does not properly take into account weights and does not differ from the unweighted case.  
  
* The update rule is currently done only if the discrepancy between desired and actual positions is above 1 when positions are actually in the "weights" scale (which can be arbitrarily small/large)  
* The update rule itself currently only takes into account the sign of the discrepancy when it has to be weighted.  
  
This implementation is correct in the unweighted case, but make the approach work poorly on situations where the weights lie far away from 1 on average (obviously when all weights are set to 1 - and one can extrapolate to an order of magnitude farther from 1 - it matches the unweighted case).  
  
This is counter-intuitive at best, and even unsatisfactory, because it is reasonable to assume that the "weighted" equivalent of an unweighted algorithm should yield similar results when presented with similar data and the same weight for each sample.  
  
Provided programs [`MWE1.{cpp,py}`](https://github.com/boostorg/accumulators/files/14981318/MWE1.zip) implement this idea:  
* Instanciate an accumulator_set of type weighted_extended_p_square_quantile and give it quantiles to track {0.001, 0.2, 0.5, 0.8, 0.999}  
* For weight in {0.0001, 0.001, 0.01, 0.1, 1., 10., 100., 1000., 10000.}  
    * Do 10000 times:  
        * Draw a sample from uniform distribution U(0, 1)  
        * Estimate quantiles {0.1, 0.35, 0.65, 0.9}  
* Plot the estimates against the truth (estimates should converge to true values reasonably fast since linear interpolation is correct with U(0,1) if quantile estimates are correct).  
  
They produce the following plot with the current implementation:  
![MWE1_current](https://github.com/boostorg/accumulators/assets/23557886/bbfcd7c9-dd65-4d00-b338-e0f62f5ba2af)  
  
As can be seen, the result highly depends on the chosen weight (small to large from left to right) and are unsatisfactory for very {small,large} weights, breaking the desirable "weight-invariance" property.  
  
Applying the proposed modifications to the heights update rule and rerunning the proposed consistency test results in a satisfactory plot:  
![MWE1_fixed](https://github.com/boostorg/accumulators/assets/23557886/566e4f93-f647-4cf9-863a-1d973e03b930)  
  
Notes:  
* Program `MWE1` can be compiled *e.g. via*: `g++ -I$BOOST_INCLUDE_PATH MWE1.cpp -o MWE1`  
* Data is generated via `MWE1 > data1.csv`  
* Plots are generated via `python3 MWE1.py`  
* `MWE1.py` requires `matplotlib` and `pandas`

---

## Comment 1

> Username: adimajo  
> Created_at: 2024-06-27 13:19:56 UTC  
> Url: https://github.com/boostorg/accumulators/pull/59#issuecomment-2194677131  

@pdimov @ericniebler

---
