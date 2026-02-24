# #10 Change broken sgi/stl links. [Merged]

> Username: tinko92  
> Created at: 2019-04-23 17:32:33 UTC  
> Updated at: 2019-04-23 19:59:46 UTC  
> Merged at: 2019-04-23 19:58:57 UTC  
> Closed at: 2019-04-23 19:58:57 UTC  
> Url: https://github.com/boostorg/functional/pull/10  

Fixes broken links to sgi.com by changing them to point to boost.org/sgi except in the case of the Allocator concept for which I could not find a page in boosts copy of that documentation which describes the actual Concept requirements and the original path in the link to sgi.com does not exist in boost.org/sgi, so I changed that to point to cppreference's definition of the Allocator concept instead.

---

## Comment 1

> Username: danieljames  
> Created_at: 2019-04-23 17:55:50 UTC  
> Url: https://github.com/boostorg/functional/pull/10#issuecomment-485908981  

Thanks, that's great. I'm not officially maintaining this nowadays, but I'm not sure if anyone else is, so if no one else chimes in, I'll merge this in a couple of days.  
  
For what it's worth there's some allocator documentation at https://www.boost.org/sgi/stl/Allocators.html but I think cppreference is probably more useful.

---

## Comment 2

> Username: tinko92  
> Created_at: 2019-04-23 18:20:25 UTC  
> Url: https://github.com/boostorg/functional/pull/10#issuecomment-485918052  

I see. Maybe I was too narrowly looking at the original link so that I was only for a concept description. For context, this is the location where the link will appear https://www.boost.org/doc/libs/1_70_0/libs/functional/factory/doc/html/index.html#boost_functional_factory.reference.factory . I'll leave the call to you then (sorry for the cop-out) regarding which reference provides sufficient information for the reader in that context.

---

## Comment 3

> Username: glenfe  
> Created_at: 2019-04-23 19:59:46 UTC  
> Url: https://github.com/boostorg/functional/pull/10#issuecomment-485952043  

Many thanks again @tinko92 for all the pull requests in multiple Boost libraries to fix these links.

---
