# #92 Add CMakeLists.txt for tests, resolve some gcc and clang warnings [Merged]

> Username: nigels-com  
> Created at: 2025-04-20 08:07:41 UTC  
> Updated at: 2025-04-20 23:16:37 UTC  
> Merged at: 2025-04-20 14:56:07 UTC  
> Closed at: 2025-04-20 14:56:07 UTC  
> Url: https://github.com/boostorg/sort/pull/92  



---

## Comment 1

> Username: nigels-com  
> Created_at: 2025-04-20 10:01:43 UTC  
> Updated_at: 2025-04-20 23:16:37 UTC  
> Url: https://github.com/boostorg/sort/pull/92#issuecomment-2817095752  

As a bit of an aside, tests building and running fine for Raspberry Pi 5 (aarch64)  
  
```  
Running tests...  
Test project /home/nigels/dev/sort/build  
      Start  1: float_sort_test  
 1/11 Test  #1: float_sort_test ..................   Passed   10.92 sec  
      Start  2: integer_sort_test  
 2/11 Test  #2: integer_sort_test ................   Passed    0.37 sec  
      Start  3: sort_detail_test  
 3/11 Test  #3: sort_detail_test .................   Passed    0.04 sec  
      Start  4: string_sort_test  
 4/11 Test  #4: string_sort_test .................   Passed    0.81 sec  
      Start  5: test_block_indirect_sort  
 5/11 Test  #5: test_block_indirect_sort .........   Passed   30.08 sec  
      Start  6: test_flat_stable_sort  
 6/11 Test  #6: test_flat_stable_sort ............   Passed    6.05 sec  
      Start  7: test_insert_sort  
 7/11 Test  #7: test_insert_sort .................   Passed    1.40 sec  
      Start  8: test_parallel_stable_sort  
 8/11 Test  #8: test_parallel_stable_sort ........   Passed   31.56 sec  
      Start  9: test_pdqsort  
 9/11 Test  #9: test_pdqsort .....................   Passed    0.84 sec  
      Start 10: test_sample_sort  
10/11 Test #10: test_sample_sort .................   Passed   29.80 sec  
      Start 11: test_spinsort  
11/11 Test #11: test_spinsort ....................   Passed    5.62 sec  
  
100% tests passed, 0 tests failed out of 11  
```

---
