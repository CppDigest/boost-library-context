# #759 Allow serializing of integer zero [Closed]

> Username: edtanous  
> Created at: 2023-06-21 16:54:54 UTC  
> Updated at: 2023-06-29 06:28:23 UTC  
> Closed at: 2023-06-29 06:28:23 UTC  
> Url: https://github.com/boostorg/url/pull/759  

Fixes #758  
  
The parsing and measuring code didn't have explicit handling for integer zero, and the while(v > 0) loop wouldn't execute.  
  
Also add tests to cover both of these cases.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-06-21 17:14:54 UTC  
> Url: https://github.com/boostorg/url/pull/759#issuecomment-1601270858  

GCOVR code coverage report [https://759.url.prtest.cppalliance.org/gcovr/index.html](https://759.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://759.url.prtest.cppalliance.org/genhtml/index.html](https://759.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://759.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://759.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 2

> Username: alandefreitas  
> Created_at: 2023-06-29 06:28:19 UTC  
> Url: https://github.com/boostorg/url/pull/759#issuecomment-1612495519  

Merged in https://github.com/boostorg/url/commit/f06f595ae760b6626764b8a01e3f8197b4016d6f

---
