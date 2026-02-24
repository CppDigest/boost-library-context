# #21 Suppress implicit instantiation of MPL end iterator [Merged]

> Username: amedama41  
> Created at: 2016-07-20 14:48:51 UTC  
> Updated at: 2016-07-21 19:34:26 UTC  
> Merged at: 2016-07-21 19:34:16 UTC  
> Closed at: 2016-07-21 19:34:16 UTC  
> Url: https://github.com/boostorg/variant/pull/21  

This change suppress implicit instantiation of MPL end iterator in the code using `boost::make_variant_over` with some MPL sequence.  
Some of the MPL iterators (for instance, boost::fusion::vector's iterator) can not be used as complete type.  
Therefore, `make_variant_over` with such an iterator results in a compile error.  
  
``` cpp  
#include <boost/fusion/container/vector.hpp>  
#include <boost/fusion/mpl.hpp>  
#include <boost/variant/variant.hpp>  
  
int main()  
{  
    boost::make_variant_over<boost::fusion::vector<int, char>>::type t{};  
}  
```  
  
This change fix the issue by avoiding the template implicit instantiation due to ADL.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2016-07-21 19:34:26 UTC  
> Url: https://github.com/boostorg/variant/pull/21#issuecomment-234359626  

Thanks for the fix!

---
