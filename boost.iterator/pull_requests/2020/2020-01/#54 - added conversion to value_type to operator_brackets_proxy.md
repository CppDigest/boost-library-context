# #54 added conversion to value_type to operator_brackets_proxy [Closed]

> Username: tobias-loew  
> Created at: 2020-01-27 17:10:33 UTC  
> Updated at: 2020-01-28 07:04:02 UTC  
> Closed at: 2020-01-28 07:04:01 UTC  
> Url: https://github.com/boostorg/iterator/pull/54  

This fix is necessary after changes to microsofts stl-implementation for the latest VS 2019 preview (16.5 preview 2): sorting a boost::multi_array with std::sort doesn't work anymore. (c.f. https://github.com/microsoft/STL/pull/289)  
The reason is that operator[] returns the internal class boost::iterators::detail::operator_brackets_proxy which cannot be converted to the iterators value_type as that would require two user-defined conversions.  
Furthermore, also the following code never worked with boost::multi_array (although it should have according to the documentation https://www.boost.org/doc/libs/1_72_0/libs/iterator/doc/html/iterator/generic.html#iterator.generic.facade.operator)  
```  
#include <boost/multi_array.hpp>  
  
void test()  
{  
    boost::multi_array<double, 2, std::allocator<double>> matrix;  
    // ...  
    auto it = matrix.begin();  
    decltype(it)::value_type t = it[0];  
}  
```

---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2020-01-27 19:06:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/iterator/pull/54#pullrequestreview-348903771  

📁 include/boost/iterator/iterator_facade.hpp

```diff
 372 |+         // it is needed in case reference is not convertible to value_type by a standard conversion sequence
 373 |+         // (e.g. boost::multi_array iterators)
 374 |+         template<typename T = value_type>
```

> Username: Lastique  
> Created_at: 2020-01-27 19:06:41 UTC  
> Updated_at: 2020-01-27 19:06:42 UTC  
> Url: https://github.com/boostorg/iterator/pull/54#discussion_r371425719  

This is C++11, Boost.Iterator must be compatible with C++03.

> Username: tobias-loew  
> Created_at: 2020-01-28 07:02:57 UTC  
> Updated_at: 2020-01-28 07:02:58 UTC  
> Url: https://github.com/boostorg/iterator/pull/54#discussion_r371635295  

Thanks for pointing that out - I'll revoke this PR anyway as microsoft/stl also revoked their change that initially broke multi_array usage with std::sort (https://github.com/microsoft/STL/pull/289)


---

## Comment 2

> Username: tobias-loew  
> Created_at: 2020-01-28 07:04:01 UTC  
> Url: https://github.com/boostorg/iterator/pull/54#issuecomment-579109312  

breaking change in other library (mcrosoft/stl) was removed

---
