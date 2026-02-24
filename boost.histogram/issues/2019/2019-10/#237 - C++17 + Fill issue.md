# #237 - C++17 + Fill issue [Closed]

> Username: henryiii  
> Created at: 2019-10-17 00:23:37 UTC  
> Updated at: 2019-10-17 10:33:02 UTC  
> Closed at: 2019-10-17 10:33:02 UTC  
> Url: https://github.com/boostorg/histogram/issues/237  

The following file:  
  
```c++  
#include <iostream>  
#include <vector>  
#include <boost/histogram.hpp>  
#include <boost/histogram/ostream.hpp>  
  
namespace bh = boost::histogram;  
  
int main() {  
  
    auto hist = bh::make_histogram(bh::axis::regular<>{2, 0, 1});  
  
    std::vector<double> arr = {.2,.3,1.2,1.2,1.4};  
    hist.fill(arr);  
  
    std::cout << hist << std::endl; // Doesn’t print bin contents, I know.  
}  
```  
  
Fails to compile with C++17, but likes C++14 just fine.  
  
```  
/Users/henryiii/git/software/histogram-python/extern/histogram/include/boost/histogram/detail/span.hpp:234:6: note:  
      candidate template ignored: substitution failure [with Container = std::__1::vector<double,  
      std::__1::allocator<double> >]: call to 'size' is ambiguous  
auto make_span(const Container& cont) {  
```  
  
Compiled with `clang++` and `g++`, both on my mac.

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-10-17 10:33:02 UTC  
> Url: https://github.com/boostorg/histogram/issues/237#issuecomment-543112949  

Thanks, already fixed in develop.
