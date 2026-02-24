# #61 Include boost/next_prior.hpp where needed [Merged]

> Username: danieljames  
> Created at: 2017-12-02 10:49:43 UTC  
> Updated at: 2017-12-02 14:00:36 UTC  
> Merged at: 2017-12-02 14:00:36 UTC  
> Closed at: 2017-12-02 14:00:36 UTC  
> Url: https://github.com/boostorg/range/pull/61  

A recent change to boost/utility.hpp removed the include of this header,  
so now it's needed for boost::prior and boost::next.  
  
https://github.com/boostorg/utility/commit/b74f49f1e56e0be7eda4085de9aa0107526b423e

---
