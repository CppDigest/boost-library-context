# #235 maximum_weighted_matching: accomodate expression templates [Closed]

> Username: sehe  
> Created at: 2020-12-16 21:21:17 UTC  
> Updated at: 2024-04-20 21:48:46 UTC  
> Closed at: 2024-04-20 21:48:46 UTC  
> Url: https://github.com/boostorg/graph/pull/235  

> weighted_augmented_path_finder with expression template  
  
As discussed in https://stackoverflow.com/a/65331081/85371  
  
Using a non-primitive weight type like  
  
    using Weight = boost::multiprecision::cpp_dec_float_50;  
  
Will run into errors because std::min fails to deduce with expression  
templates. Disabling them:  
  
    using Weight = boost::multiprecision::number<  
            boost::multiprecision::cpp_dec_float<50>,  
            boost::multiprecision::et_off >;  
  
is, of course, not optimal. This change adds the explicit template  
argument.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2021-12-06 21:13:48 UTC  
> Url: https://github.com/boostorg/graph/pull/235#issuecomment-987220978  

Hmm, interesting. Thanks for identifying this issue. It would be great to have a test that actually uses an expression template type such as from Boost.Multiprecision. Would you mind adding it?  
  
Are there any alternative ways of solving this worth considering?  
  
Btw, I'm not going to merge any PRs until I fix the CI, so please be patient.

---

## Comment 2

> Username: sehe  
> Created_at: 2021-12-06 21:32:26 UTC  
> Url: https://github.com/boostorg/graph/pull/235#issuecomment-987247162  

The linked question contains a self-contained example [Workaround #3](http://coliru.stacked-crooked.com/a/f0ce705eb7299c81) using `maximum_weighted_matching`, which needs the patch in this PR to compile with   
  
```c++  
using Weight = boost::multiprecision::cpp_dec_float_50;  
```  
  
As opposed to the version with expression templates disabled:  
  
```c++  
using Weight = boost::multiprecision::number<  
        boost::multiprecision::cpp_dec_float<50>,  
        boost::multiprecision::et_off >;  
```  
  
I don't think it's feasible to revisit all of BGL to make sure it all works fine with arbitrary precision number types (it's a combinatoric endeavour + coverage problem). However, this is a case of "low-hanging fruit" and perhaps it can serve as a nucleus/catalyst around which some more awareness can grow.

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2023-12-04 23:18:17 UTC  
> Url: https://github.com/boostorg/graph/pull/235#issuecomment-1839699556  

I want to merge a fix like this, but if there is no accompanying unit test that exercises it, then there is very little barrier to someone else breaking it again in the future. Also, a test case demonstrates that this change is complete and really does fix it -- everyone makes mistakes and forgets to include a complete change sometimes.  
  
I strongly encourage everyone that contributes to Boost.Graph to follow a basic regime for contribution:  
1. Add a test case that fails.  
2. Add your change that makes it pass.  
  
Otherwise, I have to do a lot of research to convince myself that a change is worthwhile. The test case makes it unambiguous.  
  
I know you have a lot of experience and I trust your judgement, and I appreciate the effort you have already done, but that doesn't alleviate the concerns I raised.

---
