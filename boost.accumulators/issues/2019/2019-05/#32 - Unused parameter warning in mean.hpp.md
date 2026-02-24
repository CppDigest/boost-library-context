# #32 - Unused parameter warning in mean.hpp [Open]

> Username: HDembinski  
> Created at: 2019-05-12 10:41:56 UTC  
> Updated at: 2023-08-24 18:48:57 UTC  
> Url: https://github.com/boostorg/accumulators/issues/32  

```  
In file included from ../../boost/accumulators/statistics/mean.hpp:18:  
../../boost/accumulators/statistics/count.hpp:48:57: error: unused parameter 'file_version' [-Werror,-Wunused-parameter]  
        void serialize(Archive & ar, const unsigned int file_version)  
```  
You could run the CI tests with -Werror to catch these, which is what I do in Boost.Histogram.

---

## Comment 1

> Username: wangzhe3224  
> Created at: 2023-08-24 18:48:56 UTC  
> Url: https://github.com/boostorg/accumulators/issues/32#issuecomment-1692236265  

hmm I have the same issue now..
