# #8 - string initializer_list tests [Closed]

> Username: vinniefalco  
> Created at: 2019-11-15 02:06:37 UTC  
> Updated at: 2019-12-04 03:22:04 UTC  
> Closed at: 2019-12-04 03:22:04 UTC  
> Url: https://github.com/boostorg/json/issues/8  

The tests for `initializer_list` in `string` have to be uncommented once we figure out how to make them work with varying small-buffer sizes.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-12-04 03:22:04 UTC  
> Url: https://github.com/boostorg/json/issues/8#issuecomment-561458975  

This was the solution:  
```  
#if BOOST_JSON_ARCH == 64  
# define INIT1 { 'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n' }  
# define INIT2 { 'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O' }  
#elif BOOST_JSON_ARCH == 32  
# define INIT1 { 'a', 'b', 'c', 'd', 'e', 'f' }  
# define INIT2 { 'A', 'B', 'C', 'D', 'E', 'F', 'G' }  
#else  
# error Unknown architecture  
#endif  
```  
  
Ugly, but works.
