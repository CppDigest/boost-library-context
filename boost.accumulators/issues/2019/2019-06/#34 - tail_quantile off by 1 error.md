# #34 - tail_quantile off by 1 error? [Open]

> Username: howsiwei  
> Created at: 2019-06-18 03:10:47 UTC  
> Updated at: 2019-06-18 03:12:20 UTC  
> Url: https://github.com/boostorg/accumulators/issues/34  

Consider the following program:  
```cpp  
#include <boost/accumulators/accumulators.hpp>  
#include <boost/accumulators/statistics.hpp>  
#include <cstdio>  
  
using namespace boost::accumulators;  
  
int main() {  
    accumulator_set<int, stats<tag::tail_quantile<left>>> acc(tag::tail<left>::cache_size = 3);  
    acc(3);  
    acc(4);  
    acc(5);  
  
    printf("%d\n", quantile(acc, quantile_probability = 0));  
    printf("%d\n", quantile(acc, quantile_probability = 0.9));  
}  
```  
  
On my machine it outputs  
```  
0  
terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<std::runtime_error> >'  
  what():  index n = 3 is not in valid range [0, 3)  
Aborted (core dumped)  
```  
Notice that the result for `quantile_probability = 0` is wrong too. Digging into https://www.boost.org/doc/libs/1_70_0/boost/accumulators/statistics/tail_quantile.hpp, I found that index -1 is accessed when `quantile_probability = 0`.  
  
The simplest fix seems to be:  
Change  
```  
if ( n < static_cast<std::size_t>(tail(args).size()))  
```  
to  
```  
if (1 <= n && n <= static_cast<std::size_t>(tail(args).size()))  
```  
  
with the caveat that exception would be thrown when  `quantile_probability = 0`.
