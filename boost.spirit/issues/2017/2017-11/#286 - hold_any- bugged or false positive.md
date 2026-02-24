# #286 - hold_any: bugged or false positive? [Closed]

> Username: Kojoley  
> Created at: 2017-11-22 00:36:37 UTC  
> Updated at: 2017-11-22 22:59:36 UTC  
> Closed at: 2017-11-22 18:36:30 UTC  
> Url: https://github.com/boostorg/spirit/issues/286  

```  
../boost/spirit/home/support/detail/hold_any.hpp:226:17: warning: placement new constructing an object of type 'std::__cxx11::basic_string<char>' and size '32' in a region of type 'void*' and size '8' [-Wplacement-new=]  
```  
http://www.boost.org/development/tests/develop/output/teeks99-03-dg7-1z-Docker-64on64-spirit-gcc-gnu-7~c++1z-warnings.html  
  
It looks like a false positive, but still scares me, because the author of original `hold_any` has rewritten it because of bugs:  
  
`Updated in 2011: After five years, I have rewritten the class from scratch to fix some really nasty bugs and to simplify the code.`  
https://www.codeproject.com/Articles/11250/High-Performance-Dynamic-Typing-in-C-using-a-Repla

---

## Comment 1

> Username: djowel  
> Created at: 2017-11-22 14:49:25 UTC  
> Url: https://github.com/boostorg/spirit/issues/286#issuecomment-346371666  

Best to ask @hkaiser

---

## Comment 2

> Username: hkaiser  
> Created at: 2017-11-22 15:27:44 UTC  
> Url: https://github.com/boostorg/spirit/issues/286#issuecomment-346383772  

That's a false positive. I will try to find the time to change the code to avoid the warning.

---

## Comment 3

> Username: Kojoley  
> Created at: 2017-11-22 18:36:30 UTC  
> Url: https://github.com/boostorg/spirit/issues/286#issuecomment-346438022  

It is strange, but I cannot reproduce warning locally, even with `-Wplacement-new=2`, even with the same compiler as in http://www.boost.org/development/tests/develop/output/Sandia-gcc-6-4-0-c++14-spirit-gcc-gnu-6-4-0-warnings.html#karma_stream-p3  
  
Closed in #288. Thanks!

---

## Comment 4

> Username: djowel  
> Created at: 2017-11-22 22:59:35 UTC  
> Url: https://github.com/boostorg/spirit/issues/286#issuecomment-346495523  

Thanks, @hkaiser !
