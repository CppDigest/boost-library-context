# #77 Fix GCC zero-as-null-pointer-constat warnings [Merged]

> Username: BartSiwek  
> Created at: 2020-03-28 10:45:37 UTC  
> Updated at: 2020-04-03 10:36:04 UTC  
> Merged at: 2020-04-03 10:35:52 UTC  
> Closed at: 2020-04-03 10:35:52 UTC  
> Url: https://github.com/boostorg/variant/pull/77  

Fixes GCC's zero-as-null-pointer-constat warnings.  
Resolves issue #76

---

## Comment 1

> Username: BartSiwek  
> Created_at: 2020-03-28 13:45:34 UTC  
> Url: https://github.com/boostorg/variant/pull/77#issuecomment-605449372  

Not sure what the issue with the build is but I have not touched anything that could cause the git clone to fail:  
```  
$ git clone -b $BOOST_BRANCH --depth 10 https://github.com/boostorg/boost.git $BOOST  
  
fatal: Too many arguments.  
```

---

## Comment 2

> Username: apolukhin  
> Created_at: 2020-04-03 10:36:03 UTC  
> Url: https://github.com/boostorg/variant/pull/77#issuecomment-608362035  

Many thanks for the PR!

---
