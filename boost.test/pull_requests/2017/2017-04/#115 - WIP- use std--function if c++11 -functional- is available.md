# #115 WIP: use std::function if c++11 <functional> is available [Open]

> Username: jhunold  
> Created at: 2017-04-07 09:32:59 UTC  
> Updated at: 2017-07-22 15:42:30 UTC  
> Url: https://github.com/boostorg/test/pull/115  

All except one test green.   
`test-tree-management-test` is failing as somehow the conversion or more  
exactly the display of `std::function` is not working as expected in   
`BOOST_TEST( tc1->p_test_func );` in line 118  
Tested c++03 compatibility with gcc-6.2 in c++03 mode

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2017-05-23 06:48:50 UTC  
> Url: https://github.com/boostorg/test/pull/115#issuecomment-303306112  

Hi, thanks for the PR. I fail to understand why should we move to `std::function`, is there any rationale? doesn't `boost::function` do the job?

---

## Comment 2

> Username: jhunold  
> Created_at: 2017-05-23 14:50:56 UTC  
> Url: https://github.com/boostorg/test/pull/115#issuecomment-303422969  

Current clang is starting to warn about deprecated c++03/c++98 features used in the implementation ```boost::function```. The easiest way to disable them is to simply switch to ```std::function``` if it is available. And I think that using the ```std::``` where possible is the correct long term solution.

---
