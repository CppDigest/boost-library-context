# #92 Avoid deprecated bind placeholders in global namespace [Merged]

> Username: mloskot  
> Created at: 2020-03-06 07:49:17 UTC  
> Updated at: 2020-04-01 11:43:32 UTC  
> Merged at: 2020-04-01 11:13:14 UTC  
> Closed at: 2020-04-01 11:13:14 UTC  
> Url: https://github.com/boostorg/program_options/pull/92  

This fixes numerous yet annoying compilation warnings:  
  
```  
  note: #pragma message:  
    The practice of declaring the Bind placeholders (_1, _2, ...)  
    in the global namespace is deprecated.  
    Please use <boost/bind/bind.hpp> + using namespace boost::placeholders,  
    or define BOOST_BIND_GLOBAL_PLACEHOLDERS to retain the current behavior.  
```  
  
This is somewhat related to PR #91   
  
-----  
  
Apart from obscuring the output of compilation of libraries using Boost.Test, it may be troublesome on CI builds. On many CI services every line of log counts and if log dumps become too long,   
builds are terminated.

---

## Comment 1

> Username: Lastique  
> Created_at: 2020-03-31 14:16:47 UTC  
> Url: https://github.com/boostorg/program_options/pull/92#issuecomment-606656053  

Doesn't this PR duplicate https://github.com/boostorg/program_options/pull/91 (not just relates to)? Granted, that PR does more than just silence the warnings.

---

## Comment 2

> Username: mloskot  
> Created_at: 2020-03-31 15:18:38 UTC  
> Url: https://github.com/boostorg/program_options/pull/92#issuecomment-606692976  

Well, it does, but I assumed the PR #91 is never gonna be merged as it is a mixture of unrelated changes, actual fixes with code tidying up and formatting. So, to increase chances for the fix being merged, I submitted this focused PR.

---

## Comment 3

> Username: vprus  
> Created_at: 2020-04-01 09:22:30 UTC  
> Url: https://github.com/boostorg/program_options/pull/92#issuecomment-607138056  

Thanks for the PR. Since only .cpp files are affected, I would prefer if 'using namespace boost::placehoders' were at the top level. Having this twice inside specific code blocks does not seem to be useful. Would that solve the problem?

---

## Comment 4

> Username: mloskot  
> Created_at: 2020-04-01 09:37:35 UTC  
> Url: https://github.com/boostorg/program_options/pull/92#issuecomment-607145274  

@vprus I refactored the PR as you suggest. Seems fine.

---

## Review 5 [Approved]

> Username: vprus  
> Created_at: 2020-04-01 11:13:00 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/program_options/pull/92#pullrequestreview-385486079  

Thanks, this looks good.

---
