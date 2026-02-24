# #40 Inheriting std::iterator is deprecated in c++17. [Merged]

> Username: DanielaE  
> Created at: 2017-12-27 08:37:56 UTC  
> Updated at: 2018-01-14 08:00:14 UTC  
> Merged at: 2017-12-28 23:43:48 UTC  
> Closed at: 2017-12-28 23:43:48 UTC  
> Url: https://github.com/boostorg/iterator/pull/40  

Boost's iterator.hpp is deprecated, too. Therefore get rid of all of that and replace inheritance by lifting std::iterator's members into the derived class.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Review 1 [Commented]

> Username: eldiener  
> Created_at: 2017-12-28 22:50:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/iterator/pull/40#pullrequestreview-85893959  

📁 include/boost/iterator/iterator_archetypes.hpp

```diff
 438 |+       };
 439 |   };
 440 | }
```

> Username: eldiener  
> Created_at: 2017-12-28 22:50:48 UTC  
> Url: https://github.com/boostorg/iterator/pull/40#discussion_r159008358  

I do not see the reason for this particular change. It does not seem to have anything to do with inheriting from std::iterator or including the deprecated boost/iterator.hpp.

> Username: eldiener  
> Created_at: 2017-12-28 23:06:31 UTC  
> Url: https://github.com/boostorg/iterator/pull/40#discussion_r159009498  

OK, I can see where boost::iterator is just std::iterator lifted into the boost namespace and you are replacing code which directly uses std::iterator with types in std::iterator. I do not know whether this falls under the idea of not inheriting from std::iterator in c++17 but the effect is the same so the code looks fine and I lift my previous objection.


---

## Comment 2

> Username: eldiener  
> Created_at: 2017-12-28 23:07:47 UTC  
> Url: https://github.com/boostorg/iterator/pull/40#issuecomment-354372061  

I need to test this out first locally with some compilers and if there are no problems I am willing to merge it unless anyone else has objections.

---
