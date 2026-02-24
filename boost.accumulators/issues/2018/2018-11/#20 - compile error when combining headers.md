# #20 - compile error when combining headers [Open]

> Username: pcaspers  
> Created at: 2018-11-09 19:31:07 UTC  
> Updated at: 2018-11-09 19:31:07 UTC  
> Url: https://github.com/boostorg/accumulators/issues/20  

I am using 1_66. I have two header files in two different libraries which work fine on their own, but when including both headers at once I get a compile error (with clang and msvc). A small example is given by the following code  
  
test.cpp:  
```  
// first header file  
  
#include <boost/accumulators/accumulators.hpp>  
#include <boost/accumulators/statistics/stats.hpp>  
#include <boost/accumulators/statistics/weighted_kurtosis.hpp>  
  
/* ... */  
  
typedef boost::accumulators::accumulator_set<  
    double, boost::accumulators::stats<boost::accumulators::tag::weighted_kurtosis>, double>  
    accumulator_set;  
accumulator_set acc_;  
  
/* ... */  
  
// second header file  
  
#include <boost/accumulators/statistics.hpp>  
  
/* ... */  
```  
Compiling this code with `clang++ -c test.cpp` yields  
  
```  
In file included from test.cpp:18:  
In file included from /usr/local/include/boost/accumulators/statistics.hpp:19:  
/usr/local/include/boost/accumulators/statistics/kurtosis.hpp:105:8: error: explicit specialization of 'boost::accumulators::feature_of<boost::accumulators::tag::weighted_kurtosis>' after instantiation  
struct feature_of<tag::weighted_kurtosis>  
       ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/accumulators/framework/depends_on.hpp:284:30: note: implicit instantiation first required here  
                    typename feature_of<feature_type>::type  
                             ^  
```
