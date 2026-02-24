# #140 Workarounds for older compilers. [Merged]

> Username: Flast  
> Created at: 2016-10-29 13:23:47 UTC  
> Updated at: 2016-10-30 16:28:53 UTC  
> Merged at: 2016-10-29 13:28:34 UTC  
> Closed at: 2016-10-29 13:28:34 UTC  
> Url: https://github.com/boostorg/fusion/pull/140  

Because some older compilers reject default for move ctor/assign.

---

## Comment 1

> Username: Flast  
> Created_at: 2016-10-29 13:27:26 UTC  
> Url: https://github.com/boostorg/fusion/pull/140#issuecomment-257091661  

Tested  
- GCC 6.2.1 20160916 - fedora rawhide  
  - gnu++98 / gnu++11 / gnu++14  
- Clang 3.8.0 - fedora rawhide  
  - gnu++98 / gnu++11 / gnu++14  
- GCC 4.4.5 20101112 - fedora 13  
  - gnu++98 / gnu++0x  
- MSVC  
  - 2008sp1 / 2010sp1 / 2012u5 / 2013u5 / 2015u3

---

## Review 2 [Commented]

> Username: vtnerd  
> Created_at: 2016-10-30 16:07:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/fusion/pull/140#pullrequestreview-6366340  

📁 include/boost/fusion/support/detail/and.hpp

```diff
  23 |-    
  23 |+ 
  24 |+     // This specialization is necessary to avoid MSVC-12 variadics bug.
```

> Username: vtnerd  
> Created_at: 2016-10-30 16:07:14 UTC  
> Url: https://github.com/boostorg/fusion/pull/140#discussion_r85662863  

Variadics vector is [disabled on MSVC 12](https://github.com/boostorg/fusion/blob/develop/include/boost/fusion/container/vector/detail/config.hpp#L31), should this be reverted and the test disabled for this compiler instead?

> Username: Flast  
> Created_at: 2016-10-30 16:28:53 UTC  
> Url: https://github.com/boostorg/fusion/pull/140#discussion_r85663346  

IMO, `detail::and_` can(will) be used for other purpose, so those are not contradictory.


---
