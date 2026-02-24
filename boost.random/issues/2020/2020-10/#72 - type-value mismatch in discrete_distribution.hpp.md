# #72 - type/value mismatch in discrete_distribution.hpp [Open]

> Username: ForceFaction  
> Created at: 2020-10-28 10:05:38 UTC  
> Updated at: 2020-10-28 10:05:38 UTC  
> Url: https://github.com/boostorg/random/issues/72  

[This condition](https://github.com/boostorg/random/blob/b8d5df31168a07e04a175ace751f0defa7be5e28/include/boost/random/discrete_distribution.hpp#L603) leads to   
```  
/usr/include/boost/random/discrete_distribution.hpp:603:32: error: type/value mismatch at argument 1 in template parameter list for ‘template<class First, class ... Others> using first = First’  
  603 |             if(a_iter->first < normalized_average) {  
      |                                ^~~~~~~~~~~~~~~~~~  
/usr/include/boost/random/discrete_distribution.hpp:603:32: note:   expected a type, got ‘normalized_average’  
```  
Substituting with `std::less(a_iter->first, normalized_average)` fixes this issue.
