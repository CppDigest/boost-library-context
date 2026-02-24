# #314 - BOOST_CHECK_CLOSE produces incorrect result [Closed]

> Username: Oldanko  
> Created at: 2021-07-14 18:30:15 UTC  
> Updated at: 2021-07-16 08:08:04 UTC  
> Closed at: 2021-07-16 08:08:04 UTC  
> Url: https://github.com/boostorg/test/issues/314  

Running this check  
`BOOST_CHECK_CLOSE(1.1920929e-07f, 0.0f, 0.001f);`  
Results in the following message:  
`error: in "[suite_name]/[case_name]": difference{3.40282e+38} between 1.1920929e-07f{1.1920929e-07} and 0.0f{0} exceeds 0.001%`  
  
Compiled with gcc 11.1.0-1, boost 1.76.0-1

---

## Comment 1

> Username: Oldanko  
> Created at: 2021-07-16 08:08:04 UTC  
> Url: https://github.com/boostorg/test/issues/314#issuecomment-881260309  

Realized my mistake, it doesn't make sense to use 0 in BOOST_CHECK_CLOSE.
