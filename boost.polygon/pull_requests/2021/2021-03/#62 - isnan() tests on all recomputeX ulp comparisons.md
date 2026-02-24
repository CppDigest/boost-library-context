# #62 isnan() tests on all recomputeX ulp comparisons. [Closed]

> Username: eadf  
> Created at: 2021-03-22 22:50:18 UTC  
> Updated at: 2021-03-24 00:22:37 UTC  
> Closed at: 2021-03-24 00:22:37 UTC  
> Url: https://github.com/boostorg/polygon/pull/62  

NaN > ULPS will always be false and that will prevent the position recalculation.  
  
This PR will prevent erroneous results from issues like #48.  
  
I honestly don't know if ```#include <cmath>``` is required. ```isnan()``` is part of C++11 and higher.

---

## Comment 1

> Username: eadf  
> Created_at: 2021-03-24 00:20:44 UTC  
> Url: https://github.com/boostorg/polygon/pull/62#issuecomment-805369697  

Im not so sure that the ```err_ field``` of ```robust_fpt``` actually can ever become NaN, so there is no need for this PR.  
  
And I don't think Issue #48 is an issue at all, it looks like ```err_``` intentionally becomes ```Infinity``` whenever a subtraction results in zero.   
  
There are, however, places in ```robust_fpt``` where I think ```err_``` should be set to ```infinity```.   
E.g. 0/0 will happily add  a default ```err_``` value where ```infinity``` would be more correct.

---
