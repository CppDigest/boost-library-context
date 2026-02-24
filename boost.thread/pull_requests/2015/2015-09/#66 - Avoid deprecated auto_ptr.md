# #66 Avoid deprecated auto_ptr [Merged]

> Username: BenWiederhake  
> Created at: 2015-09-19 16:25:08 UTC  
> Updated at: 2015-09-19 17:59:25 UTC  
> Merged at: 2015-09-19 17:59:25 UTC  
> Closed at: 2015-09-19 17:59:25 UTC  
> Url: https://github.com/boostorg/thread/pull/66  

This fixes the issue described in https://svn.boost.org/trac/boost/ticket/11672 ("Should use unique_ptr, not auto_ptr").  
  
EDIT: I forgot to mention: It passes all regression tests. (`fix-auto-ptr/master` also passes all regression tests.)  
  
This PR is against develop. In case you want a PR against master, see https://github.com/BenWiederhake/thread/tree/fix-auto-ptr/master

---

## Comment 1

> Username: viboes  
> Created_at: 2015-09-19 17:59:16 UTC  
> Url: https://github.com/boostorg/thread/pull/66#issuecomment-141693779  

Thanks

---
