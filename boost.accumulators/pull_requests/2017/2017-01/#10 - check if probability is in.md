# #10 check if probability is in [0, 1] [Open]

> Username: e-kwsm  
> Created at: 2017-01-10 09:21:32 UTC  
> Updated at: 2018-12-24 07:13:20 UTC  
> Url: https://github.com/boostorg/accumulators/pull/10  

* include/boost/accumulators/statistics/extended_p_square.hpp  
* include/boost/accumulators/statistics/extended_p_square_quantile.hpp  
* include/boost/accumulators/statistics/extended_p_square_quantile.hpp  
* include/boost/accumulators/statistics/p_square_quantile.hpp  
* include/boost/accumulators/statistics/peaks_over_threshold.hpp  
* include/boost/accumulators/statistics/pot_quantile.hpp  
* include/boost/accumulators/statistics/pot_tail_mean.hpp  
* include/boost/accumulators/statistics/tail_mean.hpp  
* include/boost/accumulators/statistics/tail_quantile.hpp  
* include/boost/accumulators/statistics/tail_variate_means.hpp  
* include/boost/accumulators/statistics/weighted_extended_p_square.hpp  
* include/boost/accumulators/statistics/weighted_peaks_over_threshold.hpp  
* include/boost/accumulators/statistics/weighted_tail_mean.hpp  
* include/boost/accumulators/statistics/weighted_tail_quantile.hpp  
* include/boost/accumulators/statistics/weighted_tail_variate_means.hpp

---

## Comment 1

> Username: yuvalif  
> Created_at: 2018-12-24 07:13:20 UTC  
> Url: https://github.com/boostorg/accumulators/pull/10#issuecomment-449695632  

@e-kwsm don't think you can introduce exceptions into existing code like that. also, there is the runtime check cost. Instead, would recommend adding documentation on the behavior in case of invalid probability values.

---
