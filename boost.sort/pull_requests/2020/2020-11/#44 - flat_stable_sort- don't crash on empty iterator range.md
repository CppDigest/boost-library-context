# #44 flat_stable_sort: don't crash on empty iterator range [Closed]

> Username: vedgy  
> Created at: 2020-11-02 09:09:07 UTC  
> Updated at: 2023-10-24 08:48:01 UTC  
> Closed at: 2023-10-24 08:48:01 UTC  
> Url: https://github.com/boostorg/sort/pull/44  

**NOTE**: this change fixes the crash I encountered yesterday. But I do not understand the internals of `flat_stable_sort`. So the fix may be incomplete. Perhaps the proper if less efficient fix belongs to one of `flat_stable_sort`'s constructors or member functions.  
  
**Details from the commit message**:  
Without the added iterator distance checks, the new test crashes in the first call to flat_stable_sort.  
  
Abbreviated output in Debug mode:  
```  
boost_sort: ...: Assertion `nblock > 0 and nblock < 5' failed.  
```  
  
The most interesting output line in Release mode (split into 3 lines):  
```  
unknown location(0): fatal error: in "test_main_caller( argc_ argv )":  
  memory access violation at address: 0x00000000:  
    no mapping at fault address  
```

---

## Comment 1

> Username: nigels-com  
> Created_at: 2023-08-26 06:00:28 UTC  
> Url: https://github.com/boostorg/sort/pull/44#issuecomment-1694182511  

Looks reasonable.

---

## Comment 2

> Username: nigels-com  
> Created_at: 2023-10-23 23:38:49 UTC  
> Url: https://github.com/boostorg/sort/pull/44#issuecomment-1776220298  

It appears that this is fixed, by alternative means.  
[commit](https://github.com/boostorg/sort/commit/07545798b65163b5334ba760e2fce042f2f0862f)  
  
This PR can be closed/declined, looks like.

---

## Comment 3

> Username: vedgy  
> Created_at: 2023-10-24 08:48:01 UTC  
> Url: https://github.com/boostorg/sort/pull/44#issuecomment-1776786053  

The `flat_stable_sort` test added in 531d497707f603df2eda259a0d6468aa3764a568 doesn't test small ranges as comprehensively as the test added in this pull request. But that's not a big deal.

---
