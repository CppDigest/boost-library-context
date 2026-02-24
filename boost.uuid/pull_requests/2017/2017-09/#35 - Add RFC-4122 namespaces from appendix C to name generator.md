# #35 Add RFC-4122 namespaces from appendix C to name generator [Merged]

> Username: jeking3  
> Created at: 2017-09-23 13:01:03 UTC  
> Updated at: 2017-10-18 19:50:40 UTC  
> Merged at: 2017-09-25 23:06:44 UTC  
> Closed at: 2017-09-25 23:06:44 UTC  
> Url: https://github.com/boostorg/uuid/pull/35  

Apologies for the whitespace removal in the documentation - geany must have trimmed the file.  I only made changes to the name generator section.  
  
CI builds for these changes:  
  
https://travis-ci.org/jeking3/uuid/builds/278859652  
https://ci.appveyor.com/project/jeking3/uuid/build/1.0.48-develop  
  
This fixes #34

---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2017-09-26 13:30:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/35#pullrequestreview-65213989  

📁 include/boost/uuid/name_generator.hpp

```diff
 119 |+ namespace ns {
 120 |+ 
 121 |+ static BOOST_FORCEINLINE uuid dns() {
```

> Username: Lastique  
> Created_at: 2017-09-26 13:30:38 UTC  
> Url: https://github.com/boostorg/uuid/pull/35#discussion_r141055749  

Do not use `static` qualifiers in headers. This leads to ODR issues.

> Username: jeking3  
> Created_at: 2017-09-26 17:05:49 UTC  
> Url: https://github.com/boostorg/uuid/pull/35#discussion_r141120317  

I'll fix it with the next push - thanks!


---
