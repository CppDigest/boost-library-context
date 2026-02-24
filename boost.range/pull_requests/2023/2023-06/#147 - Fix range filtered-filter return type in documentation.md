# #147 Fix range filtered/filter return type in documentation [Open]

> Username: VincentRouvreau  
> Created at: 2023-06-26 16:52:22 UTC  
> Updated at: 2023-06-26 16:52:22 UTC  
> Url: https://github.com/boostorg/range/pull/147  

I was struggling to find the return type of range filtered/filter.  
  
Here is a small working example (based from your documentation):  
```cpp  
#include <boost/range/adaptor/filtered.hpp>  
#include <boost/range/algorithm/copy.hpp>  
#include <boost/assign.hpp>  
#include <iterator>  
#include <iostream>  
#include <vector>  
  
struct is_even  
{  
    bool operator()( int x ) const { return x % 2 == 0; }  
};  
  
int main(int argc, const char* argv[])  
{  
    std::vector<int> input {1,2,3,4,5,6,7,8,9};  
      
    boost::filtered_range<decltype(is_even()), decltype(input)> f_range = boost::adaptors::filter(input, is_even());  
    boost::copy(f_range, std::ostream_iterator<int>(std::cout, ","));  
  
    return 0;  
}  
```

---
