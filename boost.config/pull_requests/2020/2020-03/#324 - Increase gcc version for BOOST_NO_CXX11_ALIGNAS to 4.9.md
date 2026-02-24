# #324 Increase gcc version for BOOST_NO_CXX11_ALIGNAS to 4.9 [Merged]

> Username: Lastique  
> Created at: 2020-03-01 10:08:27 UTC  
> Updated at: 2020-03-24 11:11:14 UTC  
> Merged at: 2020-03-24 11:06:58 UTC  
> Closed at: 2020-03-24 11:06:58 UTC  
> Url: https://github.com/boostorg/config/pull/324  

gcc 4.8 [appears](https://travis-ci.org/boostorg/atomic/jobs/656805795#L870) to not support `alignas` specifier with a constant expression as an argument. This PR updates the test to detect this and increases gcc version to 4.9.

---
