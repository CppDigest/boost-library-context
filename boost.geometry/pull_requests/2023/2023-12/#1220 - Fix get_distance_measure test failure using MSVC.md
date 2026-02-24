# #1220 Fix get_distance_measure test failure using MSVC [Merged]

> Username: FantasqueX  
> Created at: 2023-12-08 08:03:05 UTC  
> Updated at: 2024-01-12 08:57:06 UTC  
> Merged at: 2024-01-12 08:54:28 UTC  
> Closed at: 2024-01-12 08:54:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/1220  

Log is listed as follows.  
```  
Running 1 test case...  
test/algorithms/overlay/get_distance_measure.cpp(60): error: in "test_main_caller( argc_ argv )": Case: issue_1183_3 ctype: e tr_side: -1 dm_side: 1  
test/algorithms/overlay/get_distance_measure.cpp(60): error: in "test_main_caller( argc_ argv )": Case: issue_1183_4 ctype: e tr_side: -1 dm_side: 1  
test/algorithms/overlay/get_distance_measure.cpp(60): error: in "test_main_caller( argc_ argv )": Case: issue_1183_5 ctype: e tr_side: -1 dm_side: 0  
test/algorithms/overlay/get_distance_measure.cpp(60): error: in "test_main_caller( argc_ argv )": Case: issue_1183_6 ctype: e tr_side: -1 dm_side: 0  
test/algorithms/overlay/get_distance_measure.cpp(60): error: in "test_main_caller( argc_ argv )": Case: issue_1183_7 ctype: e tr_side: -1 dm_side: 0  
test/algorithms/overlay/get_distance_measure.cpp(60): error: in "test_main_caller( argc_ argv )": Case: issue_1183_8 ctype: e tr_side: -1 dm_side: 0  
test/algorithms/overlay/get_distance_measure.cpp(60): error: in "test_main_caller( argc_ argv )": Case: issue_1183_9 ctype: e tr_side: -1 dm_side: 0  
test/algorithms/overlay/get_distance_measure.cpp(60): error: in "test_main_caller( argc_ argv )": Case: issue_1183_10 ctype: e tr_side: -1 dm_side: 0  
test/algorithms/overlay/get_distance_measure.cpp(60): error: in "test_main_caller( argc_ argv )": Case: issue_1183_11 ctype: e tr_side: -1 dm_side: 0  
test/algorithms/overlay/get_distance_measure.cpp(60): error: in "test_main_caller( argc_ argv )": Case: issue_1183_12 ctype: e tr_side: -1 dm_side: 0  
  
*** 10 failures are detected in the test module "Test Program"  
```  
  
The reason is that in MSVC the long double type is identical to the double type according to [document](https://learn.microsoft.com/en-us/cpp/c-language/type-long-double?view=msvc-170). We should treat 64-bit long double as double. Modification to float is made for consistency.  
  
Full log is attached as [result.txt](https://github.com/boostorg/geometry/files/13611117/result.txt).

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2023-12-14 14:06:38 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1220#pullrequestreview-1781890866  

Thanks, LGTM!

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2024-01-11 17:42:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/1220#issuecomment-1887662604  

Thanks! Surprisingly it only shows now. There might be more placed in the code where it is checked like this.

---
