# #28 Fix compilation with hidden visibility by default [Closed]

> Username: Lastique  
> Created at: 2018-08-20 11:34:56 UTC  
> Updated at: 2018-08-20 11:37:30 UTC  
> Closed at: 2018-08-20 11:37:30 UTC  
> Url: https://github.com/boostorg/locale/pull/28  

The commit removes the useless check for __declspec presence so that symbol  
visibility attributes are applied on platforms other than Windows. This fixes  
library compilation when hidden visibility is in effect.  
  
Related to https://github.com/boostorg/boost/pull/190.

---

## Comment 1

> Username: Lastique  
> Created_at: 2018-08-20 11:37:30 UTC  
> Url: https://github.com/boostorg/locale/pull/28#issuecomment-414286191  

Duplicates https://github.com/boostorg/locale/pull/15.

---
