# #8 Fix invalid insertion hint to underlying set container [Merged]

> Username: davelgraphcore  
> Created at: 2017-02-26 08:22:26 UTC  
> Updated at: 2018-06-03 15:58:47 UTC  
> Merged at: 2018-06-03 15:58:47 UTC  
> Closed at: 2018-06-03 15:58:47 UTC  
> Url: https://github.com/boostorg/icl/pull/8  

The code in the add_segment function tries to get the prior of an  
iterator that may be the first iterator of the underlying set container.  
This is then used as an insertion hint which causes a segfault in some  
standard container libraries.  
  
The fix is to use cyclic_prior instead.  
  
This is a fix for ticket #12872.

---

## Comment 1

> Username: davelgraphcore  
> Created_at: 2017-02-26 08:23:07 UTC  
> Url: https://github.com/boostorg/icl/pull/8#issuecomment-282540786  

I'm not too familiar with the code-base so this may not be the correct fix but it does fix the issue for me. I wasn't sure where to add a test case...

---
