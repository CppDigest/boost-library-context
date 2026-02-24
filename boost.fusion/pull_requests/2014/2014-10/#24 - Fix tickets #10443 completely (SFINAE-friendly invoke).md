# #24 Fix tickets #10443 completely (SFINAE-friendly invoke). [Merged]

> Username: Flast  
> Created at: 2014-10-22 11:04:08 UTC  
> Updated at: 2014-10-22 11:11:49 UTC  
> Merged at: 2014-10-22 11:08:07 UTC  
> Closed at: 2014-10-22 11:08:07 UTC  
> Url: https://github.com/boostorg/fusion/pull/24  

I think @djowel 's fix (6cad6be) is incomplete. I propose a patch to fix completely.  
  
This PR should fix [Test output: BP x86_64 C++11 - fusion - invoke / gcc-4.8.3~c11](http://www.boost.org/development/tests/develop/developer/output/BP%20x86_64%20C++11-boost-bin-v2-libs-fusion-test-invoke-test-gcc-4-8-3~c11-debug-debug-symbols-off.html) and similar failure.  
  
Notes:  
  
```  
// A  
template <class Func, class A, class Enable = void>  
struct invoke_impl;  
  
// B  
template <class Func, class A>  
struct invoke_impl<Func, A, typename result_of<Func(A)>::type>  
{  
     typedef typename result_of<Func(A)>::type type;  
};  
  
invoke_impl<int(*)(int), int>::type  
// This doesn't match expected specialization (B), because result_of::type is `int`  
// but `Enable` is `void` (defaulted by template declaration), and compile error.  
```  
  
c.f. http://melpon.org/wandbox/permlink/ffr4clbbSgDaWlr2

---

## Comment 1

> Username: djowel  
> Created_at: 2014-10-22 11:08:01 UTC  
> Url: https://github.com/boostorg/fusion/pull/24#issuecomment-60068774  

awesome. you know your c++ very well! THANK YOU!

---

## Comment 2

> Username: Flast  
> Created_at: 2014-10-22 11:11:43 UTC  
> Url: https://github.com/boostorg/fusion/pull/24#issuecomment-60069117  

Wow! quick merging! Thank you.

---
