# #30 BoostTestJamfile: Allow multiple sources for tests [Merged]

> Username: Flamefire  
> Created at: 2023-01-01 16:55:17 UTC  
> Updated at: 2023-01-02 18:33:50 UTC  
> Merged at: 2023-01-02 18:30:13 UTC  
> Closed at: 2023-01-02 18:30:13 UTC  
> Url: https://github.com/boostorg/cmake/pull/30  

Restrict that case to alpha-numeric (plus underscore) filenames with `.cpp` ending to avoid wrongly matching any other B2 syntax elements.  
  
Closes #29   
  
Alternative implementation: Use 2 `if-MATCHES` each setting `sources` appropriately to avoid the nested RegEx. It makes sense to put the current last case (`elseif`) around those 2 so we can call `boost_message` and `continue` if neither of the 2 matches and call `boost_test` if either matched (i.e. I'd not duplicate the call to `boost_test`)

---

## Comment 1

> Username: pdimov  
> Created_at: 2023-01-02 12:57:03 UTC  
> Url: https://github.com/boostorg/cmake/pull/30#issuecomment-1368923475  

Two separate `if MATCHES` is probably better to avoid the regex growth in case we add any more. Duplicating the call to `boost_test` is fine.

---

## Comment 2

> Username: Flamefire  
> Created_at: 2023-01-02 13:40:08 UTC  
> Url: https://github.com/boostorg/cmake/pull/30#issuecomment-1368956263  

Done. I tried the non-duplicating approach and found it can be used to make the RegExes a bit shorter as we can strip of the type already. So I'd use that if you don't mind.  
  
What I found a bit surprising: We need to get a CMake list of sources out of `run foo.cpp bar.cpp ;`. My RegEx group matches on `"foo.cpp bar.cpp "` which after the replace becomes `"foo.cpp;bar.cpp;"`. So I'd think we would need to remove the trailing semicolon but it works well without and `boost_test` does receive `"foo.cpp;bar.cpp"`, i.e. the last, empty element gets dropped in the call. So this works (tested with my use case)

---

## Comment 3

> Username: pdimov  
> Created_at: 2023-01-02 15:40:42 UTC  
> Url: https://github.com/boostorg/cmake/pull/30#issuecomment-1369044412  

Looks good. Please add a test to https://github.com/boostorg/cmake/blob/develop/test/boost_test/Jamfile.

---

## Comment 4

> Username: Flamefire  
> Created_at: 2023-01-02 16:05:03 UTC  
> Url: https://github.com/boostorg/cmake/pull/30#issuecomment-1369058962  

Done:  
  
```  
-- boost_test_jamfile: Jamfile line ignored: link run_multi_2.cpp run_multi.cpp  
-- boost_test_jamfile: Jamfile line ignored: run  
-- boost_test_jamfile: Jamfile line ignored: run test_message.cpp : $(BOOST_ROOT)/subdir ;  
-- boost_test_jamfile: Jamfile line ignored: run run_multi.cpp <target-os>windows:non_existant.cpp ;  
14/18 Test #17: run-boost_cmake_test_jamfile-run_multi ................   Passed    0.00 sec  
```

---

## Comment 5

> Username: pdimov  
> Created_at: 2023-01-02 16:13:10 UTC  
> Url: https://github.com/boostorg/cmake/pull/30#issuecomment-1369066211  

Wait, why is "link run_multi_2.cpp run_multi.cpp" being ignored?

---

## Comment 6

> Username: Flamefire  
> Created_at: 2023-01-02 16:15:50 UTC  
> Url: https://github.com/boostorg/cmake/pull/30#issuecomment-1369068771  

> Wait, why is "link run_multi_2.cpp run_multi.cpp" being ignored?  
  
Oh true. Confused about the skip & ignored parts. It should not be, but the current RegEx expects a space. Fixing...

---
