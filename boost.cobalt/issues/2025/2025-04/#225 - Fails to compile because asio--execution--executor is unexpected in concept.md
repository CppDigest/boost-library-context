# #225 - Fails to compile because asio::execution::executor is unexpected in concept [Open]

> Username: niansa  
> Created at: 2025-04-12 01:36:07 UTC  
> Updated at: 2025-09-10 18:44:25 UTC  
> Url: https://github.com/boostorg/cobalt/issues/225  

Compiling cobalt via CMake gives me the following error message:  
  
```  
/tmp/boost-src/libs/cobalt/include/boost/cobalt/concepts.hpp(65,25): error: expected concept name with optional arguments  
   65 |   {t.get_executor()} -> asio::execution::executor;  
      |                         ^  
1 error generated.  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-06-20 14:33:49 UTC  
> Url: https://github.com/boostorg/cobalt/issues/225#issuecomment-2991851552  

How are you compiling?

---

## Comment 2

> Username: niansa  
> Created at: 2025-06-26 13:49:49 UTC  
> Url: https://github.com/boostorg/cobalt/issues/225#issuecomment-3008573299  

On Linux using clang-cl with Microsoft headers/libs.  
https://github.com/mstorsjo/msvc-wine <- their clang-cl setup

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2025-06-26 14:19:05 UTC  
> Url: https://github.com/boostorg/cobalt/issues/225#issuecomment-3008667702  

That's odd, the check asio does is below:  
  
```cpp  
  
// Support concepts on compilers known to allow them.  
#if !defined(BOOST_ASIO_HAS_CONCEPTS)  
# if !defined(BOOST_ASIO_DISABLE_CONCEPTS)  
#  if defined(__cpp_concepts)  
#   define BOOST_ASIO_HAS_CONCEPTS 1  
#   if (__cpp_concepts >= 201707)  
#    define BOOST_ASIO_CONCEPT concept  
#   else // (__cpp_concepts >= 201707)  
#    define BOOST_ASIO_CONCEPT concept bool  
#   endif // (__cpp_concepts >= 201707)  
#  endif // defined(__cpp_concepts)  
# endif // !defined(BOOST_ASIO_DISABLE_CONCEPTS)  
#endif // !defined(BOOST_ASIO_HAS_CONCEPTS)  
```  
  
Can you manually define BOOST_ASIO_HAS_CONCEPTS ?

---

## Comment 4

> Username: niansa  
> Created at: 2025-06-27 17:20:08 UTC  
> Url: https://github.com/boostorg/cobalt/issues/225#issuecomment-3013852981  

Someone seems to have "fixed" it like this in a `.patch` file:  
  
```patch  
 {  
-  {t.get_executor()} -> asio::execution::executor;  
+  t.get_executor();  
 };  
```  
For me at least this "fix" works.

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2025-06-27 18:26:11 UTC  
> Url: https://github.com/boostorg/cobalt/issues/225#issuecomment-3014022151  

Did you try the `BOOST_ASIO_HAS_CONCEPTS` ?

---

## Comment 6

> Username: niansa  
> Created at: 2025-08-04 11:37:35 UTC  
> Url: https://github.com/boostorg/cobalt/issues/225#issuecomment-3150251298  

Hi, sorry, I must've somehow skipped that comment suggesting `BOOST_ASIO_HAS_CONCEPTS`. Looks like it's already defined.

---

## Comment 7

> Username: crueter  
> Created at: 2025-09-10 18:44:25 UTC  
> Url: https://github.com/boostorg/cobalt/issues/225#issuecomment-3276122999  

> Can you manually define BOOST_ASIO_HAS_CONCEPTS ?  
  
I tried this earlier and it didn't seem to change anything. I don't have the capability for further tests for a little while, but I'll try again.
