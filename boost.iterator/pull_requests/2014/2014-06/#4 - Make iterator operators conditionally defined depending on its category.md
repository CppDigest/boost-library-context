# #4 Make iterator operators conditionally defined depending on its category. [Merged]

> Username: Lastique  
> Created at: 2014-06-29 20:15:24 UTC  
> Updated at: 2014-07-08 16:22:50 UTC  
> Merged at: 2014-06-30 22:49:53 UTC  
> Closed at: 2014-06-30 22:49:53 UTC  
> Url: https://github.com/boostorg/iterator/pull/4  

This makes iterators defined using iterator_facade more friendly to type inspection and fixes its use with next()/prior() since commit https://github.com/boostorg/utility/commit/651a869d4f9479dd3dfdd0293305a60b8c8d0e1c.  
  
The arithmetic, indexing and relational operators are only defined if the iterator category or traversal permits that. Note that the implementation requires partial template specialization support now.  
  
The pull request also incorporates changes from https://github.com/boostorg/iterator/pull/3.

---

## Comment 1

> Username: Belcourt  
> Created_at: 2014-06-30 18:11:59 UTC  
> Url: https://github.com/boostorg/iterator/pull/4#issuecomment-47565708  

This is a largish change but I've tested it on both Darwin and Linux and it fixes the Graph failures so I'm okay with merging this.  I still need more time to fully digest these changes.

---

## Comment 2

> Username: Lastique  
> Created_at: 2014-07-08 16:22:50 UTC  
> Url: https://github.com/boostorg/iterator/pull/4#issuecomment-48363146  

Will this change be merged to master before 1.56? This fixes Boost.Signals compilation with MSVC, see:  
  
http://lists.boost.org/Archives/boost/2014/07/215019.php

---
