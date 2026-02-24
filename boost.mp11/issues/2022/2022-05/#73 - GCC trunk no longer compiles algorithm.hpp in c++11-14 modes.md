# #73 - GCC trunk no longer compiles algorithm.hpp in c++11/14 modes [Closed]

> Username: sehe  
> Created at: 2022-05-04 13:39:00 UTC  
> Updated at: 2022-05-05 15:21:39 UTC  
> Closed at: 2022-05-05 15:21:39 UTC  
> Url: https://github.com/boostorg/mp11/issues/73  

GCC gives (potentially new) diagnostics https://compiler-explorer.com/z/TjjdserT1  
  
```c++  
/opt/compiler-explorer/libs/boost_1_78_0/boost/mp11/algorithm.hpp:445:102: error: '>=' should be '> =' to terminate a template argument list  
  445 | struct mp_take_c_impl<N, L<T1, T2, T3, T4, T5, T6, T7, T8, T9, T10, T...>, typename std::enable_if<N >= 10>::type>  
      |                                                                                                      ^~  
      |                                                                                                      > =  
/opt/compiler-explorer/libs/boost_1_78_0/boost/mp11/algorithm.hpp:445:107: error: template argument 3 is invalid  
  445 | struct mp_take_c_impl<N, L<T1, T2, T3, T4, T5, T6, T7, T8, T9, T10, T...>, typename std::enable_if<N >= 10>::type>  
      |                                                                                                           ^  
/opt/compiler-explorer/libs/boost_1_78_0/boost/mp11/algorithm.hpp:445:114: error: expected unqualified-id before '>' token  
  445 | struct mp_take_c_impl<N, L<T1, T2, T3, T4, T5, T6, T7, T8, T9, T10, T...>, typename std::enable_if<N >= 10>::type>  
      |                                                                                                                  ^  
```  
  
The diagnostics stop compilation. Older versions of GCC do not object, and neither does c++17 or higher.

---

## Comment 1

> Username: pdimov  
> Created at: 2022-05-04 14:15:25 UTC  
> Url: https://github.com/boostorg/mp11/issues/73#issuecomment-1117367278  

Looks like a GCC regression to me. Will you report it, or should I?

---

## Comment 2

> Username: sehe  
> Created at: 2022-05-04 14:18:02 UTC  
> Url: https://github.com/boostorg/mp11/issues/73#issuecomment-1117370457  

Go ahead!

---

## Comment 3

> Username: breese  
> Created at: 2022-05-04 14:38:25 UTC  
> Url: https://github.com/boostorg/mp11/issues/73#issuecomment-1117402335  

Does it work if you put a parenthesis around the condition (e.g `std::enable_if<(N >=10)>`)?

---

## Comment 4

> Username: pdimov  
> Created at: 2022-05-04 15:12:52 UTC  
> Url: https://github.com/boostorg/mp11/issues/73#issuecomment-1117461821  

https://gcc.gnu.org/bugzilla/show_bug.cgi?id=105482

---

## Comment 5

> Username: pdimov  
> Created at: 2022-05-04 15:13:08 UTC  
> Url: https://github.com/boostorg/mp11/issues/73#issuecomment-1117462525  

Parentheses will probably work, yes.

---

## Comment 6

> Username: sehe  
> Created at: 2022-05-05 15:11:32 UTC  
> Url: https://github.com/boostorg/mp11/issues/73#issuecomment-1118676410  

This is probably good to close, unless the parentheses workaround is desirable in addition to the upstream fix?

---

## Comment 7

> Username: pdimov  
> Created at: 2022-05-05 15:21:39 UTC  
> Url: https://github.com/boostorg/mp11/issues/73#issuecomment-1118690332  

I don't think a released GCC will have this bug, so there should be no need for adding parentheses.  
  
The original link no longer shows the error, so gcc trunk has been already fixed on CE. Closing.
