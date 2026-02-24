# #15 Include type_traits/remove_cv.hpp [Closed]

> Username: de-vri-es  
> Created at: 2015-05-04 13:20:54 UTC  
> Updated at: 2015-05-04 13:53:38 UTC  
> Closed at: 2015-05-04 13:53:38 UTC  
> Url: https://github.com/boostorg/variant/pull/15  

Include type_traits/remove_cv.hpp in boost/variant/detail/element_index.hpp. Otherwise including <boost/variant/get.hpp> before or without other headers will generate compile errors.

---

## Comment 1

> Username: alkino  
> Created_at: 2015-05-04 13:49:19 UTC  
> Url: https://github.com/boostorg/variant/pull/15#issuecomment-98708999  

See pull #14 which include type_traits header.

---

## Comment 2

> Username: de-vri-es  
> Created_at: 2015-05-04 13:53:38 UTC  
> Url: https://github.com/boostorg/variant/pull/15#issuecomment-98710896  

Whoopsie, only skimmed the title and didn't look. Closing this.

---
