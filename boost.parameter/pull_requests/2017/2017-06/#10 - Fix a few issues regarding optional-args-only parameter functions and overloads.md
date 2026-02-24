# #10 Fix a few issues regarding optional-args-only parameter functions and overloads [Closed]

> Username: metivett  
> Created at: 2017-06-26 13:46:13 UTC  
> Updated at: 2017-07-12 16:14:57 UTC  
> Closed at: 2017-07-12 16:14:57 UTC  
> Url: https://github.com/boostorg/parameter/pull/10  

The ```BOOST_PP_EXPR_IF(n, typename)``` preprocessor instructions lead to "missing typename" errors for functions without "required" arguments, and the ```BOOST_PARAMETER_IMPL``` gives the same name to the ```build_param_impl[fname]``` for multiple overloads, leading to "ambiguous call" errors.

---

## Comment 1

> Username: eldiener  
> Created_at: 2017-06-27 18:32:14 UTC  
> Url: https://github.com/boostorg/parameter/pull/10#issuecomment-311445777  

I would like to see some added tests illustrating the current problem(s) and showing that your change fixes the problem(s).

---

## Comment 2

> Username: eldiener  
> Created_at: 2017-06-28 05:10:17 UTC  
> Url: https://github.com/boostorg/parameter/pull/10#issuecomment-311557971  

There are current tests where where there are no "required" parameters and I am not seeing any problems, so I need to see tests with "required" parameters which exhibit the problem which your PR is supposed to fix. I am also not sure to what situations regarding multiple overloads you are referring. Here again an actual code example where you are seeing a problem is desired.

---

## Comment 3

> Username: eldiener  
> Created_at: 2017-07-09 15:31:42 UTC  
> Url: https://github.com/boostorg/parameter/pull/10#issuecomment-313926892  

I need code examples which show the bugs which this PR supposedly fixes, else I am going to close this PR.

---
