# #61 - Feature request: use all tracked probabilities to estimate a quantile [Open]

> Username: adimajo  
> Created at: 2024-04-16 07:11:02 UTC  
> Updated at: 2024-04-16 07:11:02 UTC  
> Url: https://github.com/boostorg/accumulators/issues/61  

In `[weighted_][extended_]p_square.hpp`, the p-square algorithm (an online - in the sense that it doesn't require storing all samples - quantile estimator) is implemented. Additionally:  
* a weighted version (that is, incoming samples are given a weight) is provided  
* an extended version (which allows the estimation of several quantiles) is provided.  
  
The extended version also introduces the ability to use *interpolation*:  
* Suppose an accumulator set for `[weighted_]extended_p_square_quantile` is instantiated with requested quantiles `{0.001, 0.2, 0.5, 0.8, 0.999}`  
* Internally, the accumulator set (implementing the p-square algorithm) actually estimates the following quantiles: `{0, 0.0005, 0.001, ~0.1, 0.2, 0.35, 0.5, 0.65, 0.8, ~0.9, 0.999, 1}` where quantiles 0 (resp. 1) corresponds to the min (resp. max) value seen, and values not present in requested quantiles are mid-points between requested quantiles.  
* Independently from this implementation detail, the `boost::accumulators::quantile` method can be used with this accumulator set to extract a desired quantile estimate.  
* If this desired quantile corresponds to a requested quantile, it is obviously directly returned.  
* If not, then depending on the accumulator set's constructor's parameters, a linear or quadratic interpolation is provided.  
* Irrespective of the interpolator being linear or quadratic, this interpolator:  
    * Will return nan if the desired quantile is below (resp. above) the min (resp. max) of the requested quantiles  
    * Will interpolate using estimates of the requested quantiles only (*i.e.* `{0.001, 0.2, 0.5, 0.8, 0.999}` in this example)  
  
In essence:  
* the interpolation step throws away more than half of the available data (computed at a relatively high cost)  
* the interpolator provides poor estimates in particular for mid-points that are estimated but not used, especially when the quantile function is not linear in-between requested quantiles  
  
To show this, the [`MWE2.{cpp,py}`](https://github.com/boostorg/accumulators/files/14981548/MWE2.zip) programs are provided.  
  
* Instanciate an accumulator_set of type weighted_extended_p_square_quantile and give it quantiles to track `{0.001, 0.2, 0.5, 0.8, 0.999}`.  
* Do 10000 times:  
    * Draw a sample from standard uniform distribution $\mathcal{N}(0, 1)$.  
    * Estimate quantiles `{0.0005, 0.1, 0.35, 0.65, 0.9, 0.9995}`.  
* Plot the estimates against the truth (estimates should converge to true values reasonably fast since they correspond to mid-points that are estimated by the p-square algorithm). Note that $\hat{q}$ corresponds to the current implementation, $\tilde{q}$ corresponds to the proposed use of all estimated quantiles (by applying the commit and using the `quantile_override` method proposed in `MWE2.cpp`).  
  
Note that estimates of quantiles 0.35 and 0.65 are reasonable (in some sense, they lie in the "linear regime" of the quantile function), but estimates of quantiles 0.1 and 0.9 are poor. Quantiles 0.0005 and 0.9995 could not be computed by the current implementation due its limitations (see above), returning nan.  
  
![MWE2](https://github.com/boostorg/accumulators/assets/23557886/e2f4eb8a-3c94-4abd-b95d-de472367810b)  
  
Notes:  
* Program MWE2 can be compiled e.g. via: `g++ -I$BOOST_INCLUDE_PATH -fno-access-control MWE2.cpp -o MWE2`  
* Data is generated via `MWE2 > data2.csv`  
* Plots are generated via `python3 MWE2.py`  
* `MWE2.py` requires `matplotlib` and `pandas`  
* **This commit/PR should not be merged into `develop` since it exposes the `accumulator` and the `heights` attribute as public to be able to implement the `quantile_override` method.**  
* **A "proper" fix would require delving into the `extract` namespace, which I did not, for lack of time and understanding of the possible implications of changes to that namespace.**  
* **If a maintainer is willing to give pointers to where these changes could be implemented, this PR could properly address this issue.**
