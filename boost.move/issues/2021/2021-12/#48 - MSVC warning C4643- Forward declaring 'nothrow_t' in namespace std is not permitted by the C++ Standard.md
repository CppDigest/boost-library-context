# #48 - MSVC warning C4643: Forward declaring 'nothrow_t' in namespace std is not permitted by the C++ Standard. [Closed]

> Username: Kojoley  
> Created at: 2021-12-15 23:45:18 UTC  
> Updated at: 2021-12-26 21:05:33 UTC  
> Closed at: 2021-12-26 21:05:33 UTC  
> Url: https://github.com/boostorg/move/issues/48  

```  
boost/move/detail/iterator_traits.hpp(31): warning C4643: Forward declaring 'input_iterator_tag' in namespace std is not permitted by the C++ Standard.  
boost/move/detail/iterator_traits.hpp(32): warning C4643: Forward declaring 'forward_iterator_tag' in namespace std is not permitted by the C++ Standard.  
boost/move/detail/iterator_traits.hpp(33): warning C4643: Forward declaring 'bidirectional_iterator_tag' in namespace std is not permitted by the C++ Standard.  
boost/move/detail/iterator_traits.hpp(34): warning C4643: Forward declaring 'random_access_iterator_tag' in namespace std is not permitted by the C++ Standard.  
boost/move/detail/iterator_traits.hpp(35): warning C4643: Forward declaring 'output_iterator_tag' in namespace std is not permitted by the C++ Standard.  
boost/move/detail/iterator_traits.hpp(36): warning C4643: Forward declaring 'contiguous_iterator_tag' in namespace std is not permitted by the C++ Standard.  
boost/move/make_unique.hpp(43): warning C4643: Forward declaring 'nothrow_t' in namespace std is not permitted by the C++ Standard.  
```  
https://godbolt.org/z/TWKhn6TxW

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-12-26 21:03:08 UTC  
> Url: https://github.com/boostorg/move/issues/48#issuecomment-1001241529  

Boost.Move forward declares these to avoid compilation overhead when declaring simple iterators. C4643 should be off in /W4 mode, but in any case we're going to selectively disable this warning.
