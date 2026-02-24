# #328 Stack overflow with Karma floating point generator [Merged]

> Username: octopus-prime  
> Created at: 2017-12-15 12:20:43 UTC  
> Updated at: 2017-12-18 11:04:24 UTC  
> Merged at: 2017-12-18 11:03:26 UTC  
> Closed at: 2017-12-18 11:03:26 UTC  
> Url: https://github.com/boostorg/spirit/pull/328  

https://svn.boost.org/trac10/ticket/5983  
https://svn.boost.org/trac10/ticket/12599  
  
Added suggested fix and test.  
  
Yeah, two bug trackers... See https://github.com/boostorg/spirit/pull/224

---

## Comment 1

> Username: octopus-prime  
> Created_at: 2017-12-15 19:24:21 UTC  
> Url: https://github.com/boostorg/spirit/pull/328#issuecomment-352090436  

What is this?  
  
```  
testing.capture-output bin.v2\libs\spirit\classic\test\file_iterator_tests.test\msvc-11.0\release\threadapi-win32\threading-multi\file_iterator_tests.run  
====== BEGIN OUTPUT ======  
Testing standard iterator  
Testing Windows iterator  
libs\spirit\classic\test\file_iterator_tests.cpp(172): test 'remove(TMP_FILE) == 0' failed in function 'int __cdecl main(void)'  
1 error detected.  
   
EXIT STATUS: 1   
====== END OUTPUT ======  
...failed testing.capture-output bin.v2\libs\spirit\classic\test\file_iterator_tests.test\msvc-11.0\release\threadapi-win32\threading-multi\file_iterator_tests.run...  
```

---

## Comment 2

> Username: octopus-prime  
> Created_at: 2017-12-18 11:04:23 UTC  
> Url: https://github.com/boostorg/spirit/pull/328#issuecomment-352394926  

So closing https://github.com/boostorg/spirit/pull/223 and https://github.com/boostorg/spirit/pull/224 too.

---
