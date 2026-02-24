# #97 Add <implicit-dependency>/boost//headers to root Jamfile [Closed]

> Username: mloskot  
> Created at: 2018-06-20 11:07:10 UTC  
> Updated at: 2018-06-20 20:45:29 UTC  
> Closed at: 2018-06-20 14:58:28 UTC  
> Url: https://github.com/boostorg/gil/pull/97  

### Description: what does this pull request do?  
  
For in-tree builds, this should ensure any changes of headers if dependency Boost libraries are considered and trigger compilation of dependant sources of tests and examples.  
  
### Tasklist  
  
- [ ] Review  
- [ ] Adjust for comments  
- [ ] All CI builds and checks have passed

---

## Review 1 [Approved]

> Username: stefanseefeld  
> Created_at: 2018-06-20 12:37:40 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/97#pullrequestreview-130372099  

I suppose that's good. I'd really like to be able to build Boost.GIL stand-alone, but that ain't possible with Boost.Build anyhow...  
Was there an issue you observed that made you submit this PR ? Did some Boost.GIL build not update prerequisite Boost, or not update after you made changes to other Boost headers ?

---

## Comment 2

> Username: mloskot  
> Created_at: 2018-06-20 14:58:27 UTC  
> Url: https://github.com/boostorg/gil/pull/97#issuecomment-398782283  

> Was there an issue you observed that made you submit this PR?  
  
No issues observed, just learned about this switch and thought it's a good idea for keeping the build up to date consistently.  
  
However, this change seems redundant since `$BOOST_ROOT/libs/Jamfile.v2` already defines it:  
  
```  
project boost/libs  
   : requirements <implicit-dependency>/boost//headers  
   ;  
```  
  
AFAIU, this sets it for all descendants in that directory.

---
