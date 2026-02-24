# #195 - vec_iterator member typedef iterator_concept should only be defined in C++20 [Closed]

> Username: theodelrieu  
> Created at: 2021-09-29 13:56:51 UTC  
> Updated at: 2022-01-07 21:15:04 UTC  
> Closed at: 2022-01-07 21:15:03 UTC  
> Url: https://github.com/boostorg/container/issues/195  

Hello,  
  
After upgrading Boost to 1.77.0, I had compiler errors when trying to combine `boost::container::flat_map` and `range-v3`.  
  
I'm using apple-clang 13, in C++17 mode.  
  
`vec_iterator` added the following line, with no `#ifdef` around:  
  
```cpp  
typedef std::contiguous_iterator_tag                                             iterator_concept;  
```  
  
Problem is that `std::contiguous_iterator_tag` is only defined in C++20, and it relies on a forward declaration found in `boost/move/detail/iterator_traits.hpp` (which is illegal IIRC).  
  
Since `range-v3` provides emulated concepts, it has to check the inheritence hierarchy of these tags, which requires them to be defined. As it makes use of `iterator_concept` the compiler is not happy:  
  
```  
/Users/theo/.conan/data/range-v3/0.11.0-r3/_/_/package/5ab84d6acfe1f23c4fae0ab88f26e3a396351ac9/include/concepts/concepts.hpp:878:13: error: incomplete type >'std::contiguous_iterator_tag' used in type trait expression  
          META_IS_BASE_OF(U, T) &&  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2022-01-04 15:53:02 UTC  
> Url: https://github.com/boostorg/container/issues/195#issuecomment-1004926329  

I've tried to detect if the standard library defines std::contiguous_iterator_tag in commit before forward declaring it in the commit:  
  
https://github.com/boostorg/move/commit/78f26da1f3a5a3831e9e70efe83f9c56eef94e8c  
  
and then, following your suggestion, defined vec_iterator::iterator_concept when std::contiguous_iterator tag it's defined:  
  
https://github.com/boostorg/container/commit/19e68ee05092ea3d107904a3d2b38a94494c62f5  
  
Let me know if this fix helps with the issue.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2022-01-07 21:15:03 UTC  
> Url: https://github.com/boostorg/container/issues/195#issuecomment-1007745853  

I've just checked that with the mentioned commits on Boost.Move and Boost.Container makes range-v3 happy with boost::container::vector in C++17 and C++20 modes.
