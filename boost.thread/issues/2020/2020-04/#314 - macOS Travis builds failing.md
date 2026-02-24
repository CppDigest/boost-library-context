# #314 - macOS Travis builds failing [Open]

> Username: pdimov  
> Created at: 2020-04-05 12:09:35 UTC  
> Updated at: 2020-04-05 12:09:35 UTC  
> Url: https://github.com/boostorg/thread/issues/314  

... with  
```  
testing.capture-output bin.v2/libs/thread/test/condition_variable__wait_for_p.test/clang-darwin-9.1/debug/threadapi-pthread/threading-multi/visibility-hidden/condition_variable__wait_for_p.run  
/bin/sh: line 9:  8429 Abort trap: 6           "bin.v2/libs/thread/test/condition_variable__wait_for_p.test/clang-darwin-9.1/debug/threadapi-pthread/threading-multi/visibility-hidden/condition_variable__wait_for_p" > "bin.v2/libs/thread/test/condition_variable__wait_for_p.test/clang-darwin-9.1/debug/threadapi-pthread/threading-multi/visibility-hidden/condition_variable__wait_for_p.output" 2>&1 < /dev/null  
====== BEGIN OUTPUT ======  
diff= 144818479  
max_diff= 75  
Assertion failed: (d < max_diff), function f, file libs/thread/test/sync/conditions/condition_variable/wait_for_pass.cpp, line 67.  
EXIT STATUS: 134  
====== END OUTPUT ======  
```  
```  
testing.capture-output bin.v2/libs/thread/test/condition_variable__wait_for_pred_p.test/clang-darwin-9.1/debug/threadapi-pthread/threading-multi/visibility-hidden/condition_variable__wait_for_pred_p.run  
/bin/sh: line 9:  8487 Abort trap: 6           "bin.v2/libs/thread/test/condition_variable__wait_for_pred_p.test/clang-darwin-9.1/debug/threadapi-pthread/threading-multi/visibility-hidden/condition_variable__wait_for_pred_p" > "bin.v2/libs/thread/test/condition_variable__wait_for_pred_p.test/clang-darwin-9.1/debug/threadapi-pthread/threading-multi/visibility-hidden/condition_variable__wait_for_pred_p.output" 2>&1 < /dev/null  
====== BEGIN OUTPUT ======  
Assertion failed: (t1 - t0 - milliseconds(250) < max_diff), function f, file libs/thread/test/sync/conditions/condition_variable/wait_for_pred_pass.cpp, line 77.  
EXIT STATUS: 134  
====== END OUTPUT ======  
```  
```  
testing.capture-output bin.v2/libs/thread/test/condition_variable_any__wait_for_p_lib.test/clang-darwin-9.1/debug/threadapi-pthread/threading-multi/visibility-hidden/condition_variable_any__wait_for_p_lib.run  
====== BEGIN OUTPUT ======  
libs/thread/test/sync/conditions/condition_variable_any/wait_for_pass.cpp(66): test '(d).count() < (ns(max_diff)).count()' ('146394000' < '75000000') failed in function 'void f()'  
1 error detected.  
EXIT STATUS: 1  
====== END OUTPUT ======  
```  
```  
testing.capture-output bin.v2/libs/thread/test/condition_variable_any__wait_until_p_lib.test/clang-darwin-9.1/debug/threadapi-pthread/threading-multi/visibility-hidden/condition_variable_any__wait_until_p_lib.run  
====== BEGIN OUTPUT ======  
libs/thread/test/sync/conditions/condition_variable_any/wait_until_pass.cpp(77): test '(d).count() < (ns(max_diff)).count()' ('144000000' < '75000000') failed in function 'void f()'  
1 error detected.  
EXIT STATUS: 1  
====== END OUTPUT ======  
```  
Can this be fixed?
