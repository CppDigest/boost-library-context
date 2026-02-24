# #17 - push/pull type are in non-deducible context [Closed]

> Username: alkis  
> Created at: 2018-01-29 14:15:46 UTC  
> Updated at: 2018-01-30 07:16:37 UTC  
> Closed at: 2018-01-30 07:16:37 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/17  

Because push_type and pull_type are inner types they are non-deducible which makes it hard to build generic algorithms on top of coroutines.  
  
For example given two coroutines that produce sorted values one can merge them into a single coroutine.  
  
```c++  
template <class T>  
using Stream = boost::coroutines2::coroutine<T>::pull_type;  
  
// T cannot be deduced here.  
template <class T>  
Stream<T> Merge(Stream<T> s0, Stream<T> s1);  
```  
  
If we have `coroutine_pull_type<T>` deduction works.

---

## Comment 1

> Username: olk  
> Created at: 2018-01-30 07:16:37 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/17#issuecomment-361498262  

Unfortunately I can't change the class names, but you could use:   
  
```  
template< class T >  
using Stream = boost::coroutines2::detail::pull_coroutine< T >;  
```  
  
If you take a look into coroutine.hpp you see:  
  
```  
template< typename T >  
struct coroutine {  
    using pull_type = detail::pull_coroutine< T >;  
    using push_type = detail::push_coroutine< T >;  
};  
```
