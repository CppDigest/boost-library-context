# #4 Also suppress Wself-move under gcc 13 [Merged]

> Username: ecatmur  
> Created at: 2023-08-16 00:16:01 UTC  
> Updated at: 2023-08-28 23:50:55 UTC  
> Merged at: 2023-08-28 23:50:55 UTC  
> Closed at: 2023-08-28 23:50:55 UTC  
> Url: https://github.com/boostorg/compat/pull/4  

Compiling with gcc 13.1.0:  
  
   ../libs/compat/test/shared_lock_test.cpp: In function 'void {anonymous}::move_assignment()':  
   ../libs/compat/test/shared_lock_test.cpp:378:10: error: moving 'lock' of type '{anonymous}::shared_lock_type' {aka 'boost::compat::shared_lock<dummy_lock>'} to itself [-Werror=self-move]  
     378 |     lock = std::move( lock );  
         |     ~~~~~^~~~~~~~~~~~~~~~~~~  
   ../libs/compat/test/shared_lock_test.cpp:378:10: note: remove 'std::move' call  
   cc1plus: all warnings being treated as errors

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2023-08-23 20:51:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/compat/pull/4#pullrequestreview-1592391171  

📁 test/shared_lock_test.cpp

```diff
  24 | #pragma clang diagnostic ignored "-Wself-move"
  25 | #endif
  26 |+ #if __GNUC__ >= 13
```

> Username: pdimov  
> Created_at: 2023-08-23 20:51:29 UTC  
> Url: https://github.com/boostorg/compat/pull/4#discussion_r1303528889  

This will produce a warning with -Wundef, needs to check `defined(__GNUC__)`.


---
