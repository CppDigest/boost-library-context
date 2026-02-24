# #424 Add GHA CMake install/subdir tests [Open]

> Username: pdimov  
> Created at: 2023-10-28 16:27:52 UTC  
> Updated at: 2025-12-01 08:48:48 UTC  
> Url: https://github.com/boostorg/asio/pull/424  



---

## Review 1 [Commented]

> Username: nigels-com  
> Created_at: 2025-12-01 08:48:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/asio/pull/424#pullrequestreview-3523844295  

📁 test/cmake_install_test/CMakeLists.txt

```diff
   3 |+ # See accompanying file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt
   4 |+ 
   5 |+ cmake_minimum_required(VERSION 3.5...3.16)
```

> Username: nigels-com  
> Created_at: 2025-12-01 08:48:48 UTC  
> Updated_at: 2025-12-01 08:48:49 UTC  
> Url: https://github.com/boostorg/asio/pull/424#discussion_r2576161010  

Top-level CMakeLists.txt requires cmake 3.8, probably makes sense to require the same here too.


---
