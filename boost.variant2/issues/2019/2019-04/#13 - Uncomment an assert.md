# #13 - Uncomment an assert [Closed]

> Username: akrzemi1  
> Created at: 2019-04-10 21:47:33 UTC  
> Updated at: 2021-09-11 15:13:57 UTC  
> Closed at: 2021-09-11 15:13:56 UTC  
> Url: https://github.com/boostorg/variant2/issues/13  

One assert() seems to be incorrectly commented out?  
https://github.com/pdimov/variant2/blob/70fbd17be9d03982ef80c62015cab33f99c775fb/include/boost/variant2/variant.hpp#L647  
  
In contrast, the same assert is active in the previous overload.

---

## Comment 1

> Username: pdimov  
> Created at: 2019-04-10 22:26:20 UTC  
> Url: https://github.com/boostorg/variant2/issues/13#issuecomment-481889454  

The commented asserts are in C++11 `constexpr` functions, and in C++11, a `constexpr` function can only consist of a single return statement. The functions containing uncommented asserts are `constexpr` only in C++14 mode.

---

## Comment 2

> Username: akrzemi1  
> Created at: 2019-04-11 07:13:40 UTC  
> Url: https://github.com/boostorg/variant2/issues/13#issuecomment-481995784  

I see. In that case, I think, the following implementation should work in C++11:  
  
```c++  
 return assert( ix_ == I+1 ), st1_.get( mp11::mp_size_t<I+1>() );  
```

---

## Comment 3

> Username: akrzemi1  
> Created at: 2019-04-11 07:20:33 UTC  
> Url: https://github.com/boostorg/variant2/issues/13#issuecomment-481997698  

Well, naked macro `assert()` will not work, but the following implementation worked in C++11 libraries:  
  
```c++  
#if defined NDEBUG  
# define ASSERT(EXPR) void(0)  
#else  
# define ASSERT(EXPR) ( LIKELY(EXPR) ?  void(0) : []{assert(!#EXPR);}() )  
#endif  
```  
  
Here's a real life use:  
https://github.com/akrzemi1/markable/blob/master/include/markable.hpp#L37-L43

---

## Comment 4

> Username: pdimov  
> Created at: 2021-09-11 15:13:56 UTC  
> Url: https://github.com/boostorg/variant2/issues/13#issuecomment-917423404  

https://github.com/boostorg/variant2/commit/49bff72be011da7ec549925a124267b6e617025b
