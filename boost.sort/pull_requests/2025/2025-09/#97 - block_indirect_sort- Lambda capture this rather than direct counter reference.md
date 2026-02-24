# #97 block_indirect_sort: Lambda capture this rather than direct counter reference [Merged]

> Username: nigels-com  
> Created at: 2025-09-04 00:14:52 UTC  
> Updated at: 2025-09-13 02:13:51 UTC  
> Merged at: 2025-09-13 02:13:50 UTC  
> Closed at: 2025-09-13 02:13:51 UTC  
> Url: https://github.com/boostorg/sort/pull/97  

[cppcheck](https://cppcheck.sourceforge.io/) has the follow complaint about block_indirect_sort:  
  
```  
/home/nigels/dev/boost/libs/sort/include/boost/sort/block_indirect_sort/block_indirect_sort.hpp:262:23: error: Using lambda that captures local variable 'counter' that is out of scope. [invalidLifetime]  
        function_t f1 = [&]( )  
                      ^  
/home/nigels/dev/boost/libs/sort/include/boost/sort/block_indirect_sort/block_indirect_sort.hpp:265:31: note: Lambda captures variable by reference here.  
            bscu::atomic_sub (counter, 1);  
                              ^  
/home/nigels/dev/boost/libs/sort/include/boost/sort/block_indirect_sort/blk_detail/move_blocks.hpp:166:14: note: Variable created here.  
    atomic_t counter(0);  
             ^  
/home/nigels/dev/boost/libs/sort/include/boost/sort/block_indirect_sort/block_indirect_sort.hpp:262:23: note: Using lambda that captures local variable 'counter' that is out of scope.  
        function_t f1 = [&]( )  
                      ^  
/home/nigels/dev/boost/libs/sort/include/boost/sort/block_indirect_sort/block_indirect_sort.hpp:267:31: error: Using lambda that captures local variable 'counter' that is out of scope. [invalidLifetime]  
        bk.works.emplace_back(f1);  
                              ^  
/home/nigels/dev/boost/libs/sort/include/boost/sort/block_indirect_sort/block_indirect_sort.hpp:265:31: note: Lambda captures variable by reference here.  
            bscu::atomic_sub (counter, 1);  
                              ^  
/home/nigels/dev/boost/libs/sort/include/boost/sort/block_indirect_sort/blk_detail/move_blocks.hpp:166:14: note: Variable created here.  
    atomic_t counter(0);  
             ^  
/home/nigels/dev/boost/libs/sort/include/boost/sort/block_indirect_sort/block_indirect_sort.hpp:267:31: note: Using lambda that captures local variable 'counter' that is out of scope.  
        bk.works.emplace_back(f1);  
```  
  
Which seems more legitimate a _warning_ than an _error_ to me, to be honest.  
  
However, changing the lambda capture to _this_ seems to resolve the cppcheck warning.  
  
To reproduce:  
  
```  
$ cmake .. -G Ninja -DCMAKE_BUILD_TYPE=Debug -DCMAKE_EXPORT_COMPILE_COMMANDS=ON -DBUILD_TESTING=Y -DCMAKE_CXX_CPPCHECK=cppcheck -DBOOST_INCLUDE_LIBRARIES=sort  
...  
$ ninja  
...  
```

---

## Comment 1

> Username: nigels-com  
> Created_at: 2025-09-04 03:11:51 UTC  
> Url: https://github.com/boostorg/sort/pull/97#issuecomment-3251624349  

MS copilot says:  
  
Here is a review of PR #97: "block_indirect_sort: Lambda capture this rather than direct counter reference" in boostorg/sort.  
  
---  
  
### Summary  
  
This PR addresses a cppcheck warning about capturing a local variable (`counter`) by reference in a lambda, where the variable might go out of scope (invalid lifetime). The author proposes capturing `this` in the lambda instead, and updating the member access accordingly, which resolves the warning.  
  
---  
  
### Code Diff  
  
**File:** `include/boost/sort/block_indirect_sort/block_indirect_sort.hpp`  
```diff  
-        function_t f1 = [&]( )  
+        function_t f1 = [this]( )  
         {  
             start_function ( );  
-            bscu::atomic_sub (counter, 1);  
+            bscu::atomic_sub (this->counter, 1);  
         };  
         bk.works.emplace_back(f1);  
```  
**Net effect:**  
- The lambda now captures `this` instead of capturing everything by reference.  
- Access to `counter` goes through `this->counter`.  
  
---  
  
### Rationale  
  
- **cppcheck warning:** The body explains cppcheck complained about the lambda capturing a local variable by reference, which could cause undefined behavior if the variable goes out of scope before the lambda is invoked.  
- **Fix:** Capturing `this` ensures the lambda refers to the member variable `counter` on the owning object, which should have a lifetime at least as long as the lambda's use.  
- **Build/test:** The author provides steps to reproduce the warning and confirms the fix with cppcheck.  
  
---  
  
### Review  
  
**Correctness:**    
- The change is correct and idiomatic in C++. Capturing `this` is the standard way to access member variables within lambdas, especially if the lambda could outlive the stack frame where it is created.  
  
**Safety:**    
- This avoids undefined behavior that could arise from referencing a local variable that goes out of scope.  
  
**Clarity:**    
- The change is minimal, clear, and well-motivated by the static analysis tool's warning.  
  
**Testing:**    
- The PR does not alter functionality, only addresses a static analysis warning. Normal CI and tests should suffice.  
  
---  
  
### Recommendation  
  
- **Approve.** The change is correct, safe, and resolves a legitimate static analysis warning. No further action required unless you want to discuss the warning's severity or behavior across compilers.

---
