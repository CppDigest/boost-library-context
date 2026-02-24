# #79 - Bug in `iterator_interface::operator<=>` [Closed]

> Username: dieram3  
> Created at: 2025-07-08 07:06:39 UTC  
> Updated at: 2025-07-13 21:00:18 UTC  
> Closed at: 2025-07-13 21:00:07 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/79  

`operator<=>` gives results in the opposite order. E.g. `std::is_lt(it <=> it + 1)` yields `false`.  
  
That's because the following line:  
  
https://github.com/boostorg/stl_interfaces/blob/e60873635eb2a4f699daee0f721d5f3cb041e826/include/boost/stl_interfaces/iterator_interface.hpp#L861  
  
should be `lhs - rhs` instead of `rhs - lhs`.  
  
Also, for what is worth, I think the full function could be simplified to  
  
```cpp  
{ return (lhs - rhs) <=> typename D1::difference_type(0); }  
```  
  
unless it is not possible due to some old compiler limitation or something similar.  
  
## Background  
  
I found this because I got a failed assertion `assert(first <= last)`, where `first` and `last` are `reverse_iterator<I>`, and `I` is an iterator implemented with `iterator_interface`. In libstdc++, there is a non-standard overload for `operator<=>` that takes precedence, and therefore it led me to encoutering issue.

---

## Comment 1

> Username: tzlaine  
> Created at: 2025-07-13 21:00:18 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/79#issuecomment-3067298534  

Fixed.  Thanks!
