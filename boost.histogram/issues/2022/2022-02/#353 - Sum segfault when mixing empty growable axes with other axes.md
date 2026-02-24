# #353 - Sum segfault when mixing empty growable axes with other axes [Closed]

> Username: henryiii  
> Created at: 2022-02-21 21:37:36 UTC  
> Updated at: 2022-07-20 07:51:16 UTC  
> Closed at: 2022-07-20 07:51:16 UTC  
> Url: https://github.com/boostorg/histogram/issues/353  

If you mix an empty categorical axes with a fairly normal axis (one that is not another similar categorical axes either empty or not - that seems to be okay), you get a segfault when trying to check the inner sum.  
  
Discovery via default repr in Hist in https://github.com/scikit-hep/hist/issues/337, also affects boost-histogram (both have the same default repr) and sums on Boost.Histogram.  
  
```cpp  
#include <boost/histogram.hpp>  
#include <boost/histogram/accumulators/ostream.hpp>  
#include <iostream>  
  
namespace bh = boost::histogram;  
  
using category_str_growth  
    = bh::axis::category<std::string, bh::use_default, bh::axis::option::growth_t>;  
  
int main() {  
    auto h = bh::make_weighted_histogram(bh::axis::regular{10,0,1}, category_str_growth{});  
  
    std::cout << bh::algorithm::sum(h, bh::coverage::all) << std::endl;  // OK  
    std::cout << bh::algorithm::sum(h, bh::coverage::inner) << std::endl; // Crash  
  
    return 0;  
}  
```

---

## Comment 1

> Username: henryiii  
> Created at: 2022-02-22 16:42:41 UTC  
> Url: https://github.com/boostorg/histogram/issues/353#issuecomment-1047993737  

Interesting, `Assertion failed: (iter_ < indices_.hist_->end()), function operator++, file indexed.hpp, line 258.` is produced when you try to do an inner sum over this if you remove the growth on the category. Ideally this should return 0 in the correct (storage dependent) type, but the assertion is better than the segfault.

---

## Comment 2

> Username: HDembinski  
> Created at: 2022-07-18 16:31:05 UTC  
> Url: https://github.com/boostorg/histogram/issues/353#issuecomment-1187713433  

indexed iteration is broken when the last axis has size 0. Work on fixing this started in #356
