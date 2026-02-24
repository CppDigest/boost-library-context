# #33 - Is there interest for improving the efficiency of the `any_cast` for the case when the `-fno-rtti` flag is set [Closed]

> Username: freak82  
> Created at: 2025-11-20 12:54:29 UTC  
> Updated at: 2026-01-04 19:00:06 UTC  
> Closed at: 2026-01-04 19:00:06 UTC  
> Url: https://github.com/boostorg/any/issues/33  

Hi there,  
  
As far as I looked at the code of `boost::any` currently it uses `boost::typeindex` for the cast functionality.  
When the RTT information is turned off via `-fno-rtti` this causes the cast to do `strcmp` internally ([link to godbolt](https://godbolt.org/z/aKoG4ncrz)) and leads to less efficient code than the `libstdc++` implementation which does only function pointer comparison in this case ([link to the implementation](https://github.com/gcc-mirror/gcc/blob/0b74386b61a05a45cf16bd09eb566e189bd21987/libstdc%2B%2B-v3/include/std/any#L536)).  
And `boost::any` already uses similar technique with function pointer for its internal operations.  
So, my questions are:  
1. Is there some downside, that you can think of, in the `libstdc++` approach versus the `boost::any/typeindex` approach? Can the comparison of the function pointer lead to wrong casts in some edge cases?  
2. Assuming that the `libstdc++` approach has no downsides, is there an interest for changing the implementation of `boost::any`?  
  
Regards,  
Pavel.

---

## Comment 1

> Username: apolukhin  
> Created at: 2025-12-04 15:54:07 UTC  
> Url: https://github.com/boostorg/any/issues/33#issuecomment-3612930116  

> Can the comparison of the function pointer lead to wrong casts in some edge cases?  
  
That's an interesting question. The function pointers could be compared differently if  
* the function is not marked as BOOST_SYMBOL_VISIBLE and one of the instances in shared object compiled with visibility=hidden, or  
* if the function is a private module part and is mixed with other translation unit (not sure if that is not an ODR violation and we should care about such cases)  
  
Take a look at the typeinfo comparison operator implementation https://github.com/llvm/llvm-project/blob/4c17f9b68426f1a01989bfc185ff50174d53f271/libcxx/src/typeinfo.cpp#L18C25-L18C41 It uses the same approach as Boost.TypeIndex and the same approach as libstdc++ https://github.com/gcc-mirror/gcc/blob/56c6612598a07514a65ad5c0b25d12d1fc966e4c/libstdc%2B%2B-v3/libsupc%2B%2B/typeinfo#L210  
  
I'm not convinced that optimizing the `any_cast` by just unconditionally comparing function pointers is worth effort, because  
* the hot path is that the types are equal  
* it has drawbacks (that's why all the typeinfo::operator== use strcmp)   
  
However, there's a profitable optimization! Take a look at the following lines:  
  
https://github.com/boostorg/type_index/blob/f2a3bdd29cc20ca2c4f9ee0916b7da6dbaaaaa28/include/boost/type_index/ctti_type_index.hpp#L152-L156  
  
The commented out `/*left == right ||*/` is usually true for equal types, but it was commented out because comparing pointers at compile time is not a constant expression.  
  
So the optimization would be to `if (left == right) return true;` if the function is not constant evaluated. This would optimize the any_cast when the -fno-rtti flag is set case.  
  
I'd appreciate a PR with the above change :)

---

## Comment 2

> Username: apolukhin  
> Created at: 2026-01-04 10:22:21 UTC  
> Updated at: 2026-01-04 10:25:43 UTC  
> Url: https://github.com/boostorg/any/issues/33#issuecomment-3707945956  

@freak82 I went further an implemented the above optimization in https://github.com/apolukhin/type_index/pull/23
