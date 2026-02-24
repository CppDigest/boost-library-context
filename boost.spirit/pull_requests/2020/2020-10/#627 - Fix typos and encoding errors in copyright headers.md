# #627 Fix typos and encoding errors in copyright headers. [Merged]

> Username: giomasce  
> Created at: 2020-10-11 15:31:47 UTC  
> Updated at: 2021-01-03 09:46:15 UTC  
> Merged at: 2021-01-03 09:46:15 UTC  
> Closed at: 2021-01-03 09:46:15 UTC  
> Url: https://github.com/boostorg/spirit/pull/627  



---

## Review 1 [Commented]

> Username: Kojoley  
> Created_at: 2020-10-12 15:31:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/627#pullrequestreview-506722330  

📁 include/boost/spirit/home/x3/support/subcontext.hpp

```diff
   2 |     Copyright (c) 2001-2014 Joel de Guzman
   3 |-     Copyright (c) 2013 Agustin Berge
   3 |+     Copyright (c) 2013 Agustín Bergé
```

> Username: Kojoley  
> Created_at: 2020-10-12 15:31:37 UTC  
> Updated_at: 2021-01-03 08:25:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/627#discussion_r503374552  

What is wrong here? It was intentionally ASCIIfied in 658ac180986240c75c2485df88354e83d8027b89.

> Username: giomasce  
> Created_at: 2020-10-12 15:52:43 UTC  
> Updated_at: 2021-01-03 08:25:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/627#discussion_r503386712  

Sorry, I missed this point. I originally wrote the patch before that change was committed, and failed to notice it when I rebased it.

> Username: djowel  
> Created_at: 2021-01-03 00:25:36 UTC  
> Updated_at: 2021-01-03 08:25:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/627#discussion_r550937024  

Can we fix this now, so we can merge this in?

> Username: giomasce  
> Created_at: 2021-01-03 08:26:30 UTC  
> Url: https://github.com/boostorg/spirit/pull/627#discussion_r550973934  

I pushed a fixed version of the commit.


---

## Comment 2

> Username: djowel  
> Created_at: 2021-01-03 09:46:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/627#issuecomment-753591967  

Many thanks!

---
