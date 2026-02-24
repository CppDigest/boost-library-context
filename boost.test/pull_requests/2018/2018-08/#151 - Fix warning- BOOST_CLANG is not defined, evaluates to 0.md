# #151 Fix warning: BOOST_CLANG is not defined, evaluates to 0 [Closed]

> Username: mloskot  
> Created at: 2018-08-24 13:41:50 UTC  
> Updated at: 2019-02-18 10:58:45 UTC  
> Closed at: 2018-09-03 19:56:44 UTC  
> Url: https://github.com/boostorg/test/pull/151  

The diagnostic is issued when compiled with GCC/clang flag `-Wundef`.  
  
Although in case `BOOST_CLANG` is not defined, ie. is not a macro and `BOOST_CLANG` identifier is considered to be zero, the `#ifdef` directive makes the intention clearer.

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2018-09-02 15:41:16 UTC  
> Url: https://github.com/boostorg/test/pull/151#issuecomment-417939485  

Wouldn't   
  
```  
#if defined(BOOST_CLANG) && (BOOST_CLANG == 1)  
```  
  
be safer? I updated the branch `topic/PR-151-BOOST_CLANG-define` accordingly.

---

## Comment 2

> Username: mloskot  
> Created_at: 2018-09-02 21:36:36 UTC  
> Url: https://github.com/boostorg/test/pull/151#issuecomment-417961524  

Or, `BOOST_CLANG != 0`. Sure, why not.

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2018-09-03 05:10:08 UTC  
> Url: https://github.com/boostorg/test/pull/151#issuecomment-418001608  

Merged to next.

---

## Comment 4

> Username: raffienficiaud  
> Created_at: 2018-09-03 19:56:44 UTC  
> Url: https://github.com/boostorg/test/pull/151#issuecomment-418185001  

Thanks, merged to develop

---

## Comment 5

> Username: mloskot  
> Created_at: 2018-09-03 20:11:33 UTC  
> Url: https://github.com/boostorg/test/pull/151#issuecomment-418186729  

Thanks!

---
