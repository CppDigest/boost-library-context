# #31 - C++11 compilation warning: use of the 'deprecated' attribute is a C++14 extension [Closed]

> Username: mloskot  
> Created at: 2019-03-21 16:04:14 UTC  
> Updated at: 2019-03-21 17:00:11 UTC  
> Closed at: 2019-03-21 16:29:41 UTC  
> Url: https://github.com/boostorg/mp11/issues/31  

Compilation of a MP11-enabled program in C++11 mode with clang spits the following warning  
  
```  
./boost/mp11/utility.hpp:223:47: warning: use of the 'deprecated' attribute is a C++14 extension [-Wc++14-extensions]  
template<class Q, class... T> using mp_invoke BOOST_MP11_DEPRECATED("please use mp_invoke_q") = mp_invoke_q<Q, T...>;  
                                              ^  
./boost/mp11/detail/config.hpp:136:41: note: expanded from macro 'BOOST_MP11_DEPRECATED'  
#   define BOOST_MP11_DEPRECATED(msg) [[deprecated(msg)]]  
                                        ^  
```  
  
For example, https://travis-ci.org/mloskot/gil/jobs/509457000

---

## Comment 1

> Username: pdimov  
> Created at: 2019-03-21 16:23:08 UTC  
> Url: https://github.com/boostorg/mp11/issues/31#issuecomment-475301052  

You wanted Clang to be `-pedantic`, you got pedantry.

---

## Comment 2

> Username: mloskot  
> Created at: 2019-03-21 16:29:41 UTC  
> Updated at: 2019-03-21 16:29:55 UTC  
> Url: https://github.com/boostorg/mp11/issues/31#issuecomment-475303783  

I see. I failed to make the connection. Thanks.

---

## Comment 3

> Username: pdimov  
> Created at: 2019-03-21 16:44:12 UTC  
> Url: https://github.com/boostorg/mp11/issues/31#issuecomment-475309700  

I didn't know about it either. I also didn't know that `warnings=all` set `-pedantic` and thought that Clang is stupid to issue this warning in `-Wall` or even `-Wextra`. Well, it turned out that Clang wasn't stupid after all. :-)  
  
There's work being done on `b2` to fix this: https://github.com/boostorg/build/commit/6611aebf3c799216d34138166b5b5923d072a8f4

---

## Comment 4

> Username: mloskot  
> Created at: 2019-03-21 17:00:11 UTC  
> Url: https://github.com/boostorg/mp11/issues/31#issuecomment-475316264  

Days of me acknowledging (my) stupidity of `-pedantic` or explicit `-W` flags in `Jamfile` are close.
