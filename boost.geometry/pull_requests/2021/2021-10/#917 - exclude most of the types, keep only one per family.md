# #917 [test] exclude most of the types, keep only one per family [Merged]

> Username: barendgehrels  
> Created at: 2021-10-08 13:22:39 UTC  
> Updated at: 2022-05-22 07:30:55 UTC  
> Merged at: 2021-10-08 14:28:09 UTC  
> Closed at: 2021-10-08 14:28:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/917  

as discussed

---

## Comment 1

> Username: awulkiew  
> Created_at: 2021-10-19 12:20:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/917#issuecomment-946665648  

It fixed the timeout issue thanks! This however exposed another issue with gcc-6.3 at CircleCI:  
https://app.circleci.com/pipelines/github/boostorg/geometry/227/workflows/1c7a92bd-934a-488a-acce-e09281ee9733/jobs/14459  
```  
libs/geometry/test/algorithms/set_operations/union/union_other_types.cpp(75): error: in "test_main_caller( argc_ argv )": union: case_multi_rectangular area: expected: 33125 detected: 10001 type: m (N5boost14multiprecision6numberINS0_8backends16rational_adaptorINS2_15cpp_int_backendILj0ELj0ELNS0_16cpp_integer_typeE1ELNS0_18cpp_int_check_typeE0ESaIyEEEEELNS0_26expression_template_optionE1EEE)  
libs/geometry/test/algorithms/set_operations/union/union_other_types.cpp(75): error: in "test_main_caller( argc_ argv )": union: case_multi_diagonal area: expected: 5350 detected: 6201 type: m (N5boost14multiprecision6numberINS0_8backends16rational_adaptorINS2_15cpp_int_backendILj0ELj0ELNS0_16cpp_integer_typeE1ELNS0_18cpp_int_check_typeE0ESaIyEEEEELNS0_26expression_template_optionE1EEE)  
```

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2021-10-20 08:46:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/917#issuecomment-947457310  

> It fixed the timeout issue thanks! This however exposed another issue with gcc-6.3 at CircleCI: https://app.circleci.com/pipelines/github/boostorg/geometry/227/workflows/1c7a92bd-934a-488a-acce-e09281ee9733/jobs/14459  
>   
> ```  
> libs/geometry/test/algorithms/set_operations/union/union_other_types.cpp(75): error: in "test_main_caller( argc_ argv )": union: case_multi_rectangular area: expected: 33125 detected: 10001 type: m (N5boost14multiprecision6numberINS0_8backends16rational_adaptorINS2_15cpp_int_backendILj0ELj0ELNS0_16cpp_integer_typeE1ELNS0_18cpp_int_check_typeE0ESaIyEEEEELNS0_26expression_template_optionE1EEE)  
> libs/geometry/test/algorithms/set_operations/union/union_other_types.cpp(75): error: in "test_main_caller( argc_ argv )": union: case_multi_diagonal area: expected: 5350 detected: 6201 type: m (N5boost14multiprecision6numberINS0_8backends16rational_adaptorINS2_15cpp_int_backendILj0ELj0ELNS0_16cpp_integer_typeE1ELNS0_18cpp_int_check_typeE0ESaIyEEEEELNS0_26expression_template_optionE1EEE)  
> ```  
  
Interesting... I didn't expect failures in Boost Multiprecision only...  
So what should we do with it? Exclude it for gcc-6.3? Because the answer is wrong, the compiler is old, and the problem might be located somewhere very deep in the code (maybe even in Boost MP).

---

## Comment 3

> Username: vissarion  
> Created_at: 2021-10-20 09:00:11 UTC  
> Updated_at: 2021-10-20 09:00:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/917#issuecomment-947467940  

+1 for excluding `gcc-6.3` but keep the issue open. (EDIT: I mean create an issue)  
Out of curiosity, does other MP types give wrong results with this compiler ?

---

## Comment 4

> Username: awulkiew  
> Created_at: 2021-10-20 10:44:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/917#issuecomment-947545695  

At CircleCI only one compiler is tested. From the fact that it fails there we don't know anything about other compilers. So we shouldn't just disable it not knowing if it's compiler's fault.  
  
For this we have to see the Boost test matrix:  
https://www.boost.org/development/tests/develop/developer/geometry.html  
and it seems that it fails with the majority of compilers if not all of them (some may not be tested yet) besides gcc-5 which has a different issue.  
  
And yes, the problem might be located in MP. It might be some issue introduced by an unrelated change, e.g. in strategies, etc. We'd have to check this to know exactly.  
  
**I compared the results compiling with MP version from master (I'm using it normally) and develop branch and it started to fail after switching branches. So this is most probably an issue introduced in MP in develop branch recently.**

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2021-10-20 11:43:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/917#issuecomment-947584506  

> At CircleCI only one compiler is tested. From the fact that it fails there we don't know anything about other compilers. So we shouldn't just disable it not knowing if it's compiler's fault.  
  
Right, good to know!  
From MP it's just the `bm::cpp_rational` if I see it correctly. So maybe we should exclude that. I'm also using master - if it's something in develop, we should create an issue for MP but it's hard to point exactly where it fails...

---

## Comment 6

> Username: awulkiew  
> Created_at: 2021-10-20 12:02:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/917#issuecomment-947596952  

So it fails for `rational_adaptor<cpp_int_backend<>>` but not for `boost::rational<>`. Maybe this will be enough to guide them to find the issue. Another simple thing would be to pinpoint the exact commit of MultiPrecision (or at least merge commit) introducing this issue.

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2021-10-20 12:11:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/917#issuecomment-947603743  

See #927   
And yes, it's good to pinpoint it - but I can't do that today

---
