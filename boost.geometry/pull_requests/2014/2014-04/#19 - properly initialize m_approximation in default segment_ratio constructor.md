# #19 [policies][robustness] properly initialize m_approximation in default segment_ratio constructor [Merged]

> Username: mkaravel  
> Created at: 2014-04-28 07:20:13 UTC  
> Updated at: 2014-04-28 09:27:40 UTC  
> Merged at: 2014-04-28 09:27:40 UTC  
> Closed at: 2014-04-28 09:27:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/19  

m_approximation is not initialized by the default constructor.  
  
Among other things, this creates a problem when comparing the default constructed segment ratio with another segment ratio. In the comparison the approximation is initially used, and since the approximation is not initialized, the result of the comparison is not predictable (can vary from run to run of the same algorithm).

---
