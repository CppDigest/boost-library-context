# #102 - Implementation of 'chop' for the Chebyshev transform [Closed]

> Username: NAThompson  
> Created at: 2018-01-21 01:06:54 UTC  
> Updated at: 2019-08-18 16:13:33 UTC  
> Closed at: 2019-08-18 16:13:33 UTC  
> Url: https://github.com/boostorg/math/issues/102  

When we implemented the truncation of the Chebyshev transform, it was based on a heuristic of Trefethen, and I believe it works very well. However, Trefethen has analyzed this problem in more detail:  
  
https://people.maths.ox.ac.uk/trefethen/aurentz_trefethen_toms_final.pdf  
  
I need to read this paper and make sure our Chebyshev series truncation is state-of-the-art.

---

## Comment 1

> Username: NAThompson  
> Created at: 2019-08-18 16:13:33 UTC  
> Url: https://github.com/boostorg/math/issues/102#issuecomment-522334771  

Our current implementation of this feature is nearly identical to what Trefethen proposed as it stands.
