# #73 Apply `use std::swap` idiom for all calls to `std::swap` [Merged]

> Username: melton1968  
> Created at: 2023-06-06 23:43:09 UTC  
> Updated at: 2023-06-09 23:09:30 UTC  
> Merged at: 2023-06-09 23:09:29 UTC  
> Closed at: 2023-06-09 23:09:30 UTC  
> Url: https://github.com/boostorg/sort/pull/73  

These changes address issue #72.  
  
I replaced all explicit calls to `std::swap` with,  
```c++  
using std::swap;  
swap(a, b);  
```  
I tried to minimize the number of code changes while keeping the `using std::swap` directives close to the calls to `swap`. They are often on consecutive lines, but in functions with several calls to `swap`, I placed a single using directive at the start of the nearest block. This was a judgment call.  
  
This brings the code up to date with `C++20` which does not allow adding template function specializations to the `std` namespace while maintaining compatibility with existing code.  
  
There is the possibility that a lookup that previously found `swap` for a user-defined type in `std` will now find a different version in the type's namespace using ADL. In theory, this will only happen if you have defined two different `swap` implementations and put one in `std` and one in the type's namespace.  
  
All the tests passed without error:  
```  
Running 1 test case...  
  
*** No errors detected  
Running 1 test case...  
  
*** No errors detected  
Running 1 test case...  
  
*** No errors detected  
Running 1 test case...  
  
*** No errors detected  
Running 1 test case...  
  
*** No errors detected  
Running 1 test case...  
  
*** No errors detected  
Running 1 test case...  
1, 2, 3, 4, 5,  
8, 6, 10, 4, 2, 3, 5, 7, 9, 11, 13, 15, 17, 19,  
8, 6, 10, 4, 2, 13, 15, 17, 19, 21, 23, 35, 27, 29,  
3, 5, 7, 9, 11, 13, 15, 17, 19, 28, 26, 30, 24, 22,  
  
*** No errors detected  
Running 1 test case...  
  
*** No errors detected  
Running 1 test case...  
  
*** No errors detected  
Running 1 test case...  
  
*** No errors detected  
Running 1 test case...  
1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 23, 25, 27, 29, 31, 3\  
3, 35, 37, 39, 41, 43, 45, 47, 49, 51, 53, 55, 57, 59,  
1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 23, 25, 27, 29, 31, 3\  
3, 35, 37, 39, 41, 43, 45, 47, 49, 51, 53, 55, 57, 59,  
  
*** No errors detected  
```

---

## Review 1 [Approved]

> Username: spreadsort  
> Created_at: 2023-06-08 23:07:26 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/sort/pull/73#pullrequestreview-1470882062  

Thanks!

---
