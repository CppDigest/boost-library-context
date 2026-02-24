# #452 Add strided_iterator class (skip_iterator) #121 [Merged]

> Username: jszuppe  
> Created at: 2015-04-12 20:07:44 UTC  
> Updated at: 2015-04-22 12:13:22 UTC  
> Merged at: 2015-04-21 02:58:31 UTC  
> Closed at: 2015-04-21 02:58:31 UTC  
> Url: https://github.com/boostorg/compute/pull/452  

This addresses issue #121.  
  
I've added `strided_iterator` adaptor which - as requested - skips over multiple elements each time it is incremented. It's called strided_iterator to match corresponding iterator in the Boost.Range library (see [boost/range/adaptor/strided.hpp](http://www.boost.org/doc/libs/1_58_0/boost/range/adaptor/strided.hpp)). Tests included.   
  
I've also included function `make_strided_iterator_end()`. It may be useful. What do you think about it?  
  
@kylelutz, thank you for providing your code. It was helpful.

---

## Comment 1

> Username: kylelutz  
> Created_at: 2015-04-15 03:18:27 UTC  
> Url: https://github.com/boostorg/compute/pull/452#issuecomment-93172347  

Looks great! I wonder if we should follow the API in boost/range/adaptor/strided.hpp which has separate `make_begin_strided_iterator()` and `make_end_strided_iterator()` functions? Or alternatively a single `make_strided_iterator()` function which then always takes both the begin and end of the underlying range.

---

## Comment 2

> Username: jszuppe  
> Created_at: 2015-04-15 09:55:14 UTC  
> Url: https://github.com/boostorg/compute/pull/452#issuecomment-93294655  

In current solution I wanted to be consistent with other iterators in Boost.Compute, which don't really need to know what is below them and where it ends. `make_strided_iterator_end()` in this context is just a helper function to find past-the-end element, so caller doesn't have to calculate it.

---

## Comment 3

> Username: jszuppe  
> Created_at: 2015-04-15 18:11:10 UTC  
> Url: https://github.com/boostorg/compute/pull/452#issuecomment-93509858  

`make_strided_iterator()` function which takes both the begin and end of the underlying iterator must led to `strided_iterator` implementation where increment method would look like this:  
  
``` cpp  
void increment()  
{  
   // super_type::base_reference() - underlying iterator referring  
   // end - iterator referring to past-the-end element   
   auto step = (std::min)(m_stride, end - super_type::base_reference());  
   std::advance(super_type::base_reference(), step);  
}  
```  
  
Otherwise why we would require end when making strided iterator for begin? And I think only that way we would get strided iterator end for `make_strided_iterator(end, end, stride)`.  
  
In this version end for `strided_iterator` made with `stride = x` and `stride = y` (where `x != y`) would be the same. My first thought is that can cause problems when someone want to iterate from the end to the begin.

---

## Comment 4

> Username: kylelutz  
> Created_at: 2015-04-21 02:58:28 UTC  
> Url: https://github.com/boostorg/compute/pull/452#issuecomment-94622821  

That makes sense. Thanks for working on this!

---
