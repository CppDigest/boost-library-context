# #17 Silence MSVC warnings about 'this' in base class initializer list. [Merged]

> Username: Lastique  
> Created at: 2017-09-05 18:39:04 UTC  
> Updated at: 2017-09-14 12:13:22 UTC  
> Merged at: 2017-09-06 12:55:24 UTC  
> Closed at: 2017-09-06 12:55:24 UTC  
> Url: https://github.com/boostorg/system/pull/17  



---

## Comment 1

> Username: Beman  
> Created_at: 2017-09-06 14:03:53 UTC  
> Url: https://github.com/boostorg/system/pull/17#issuecomment-327493380  

Thanks Andrey and Peter,  
  
--Beman

---

## Comment 2

> Username: Lastique  
> Created_at: 2017-09-13 10:51:52 UTC  
> Url: https://github.com/boostorg/system/pull/17#issuecomment-329131629  

The tests are green, can this PR be merged to master, please?

---

## Comment 3

> Username: pdimov  
> Created_at: 2017-09-13 11:50:22 UTC  
> Url: https://github.com/boostorg/system/pull/17#issuecomment-329143618  

This is not the only difference between develop and master, so I've been assuming that Beman would take care of merging them.  
  
But I can cherry-pick this specific commit if it's urgent to do so.

---

## Comment 4

> Username: Lastique  
> Created_at: 2017-09-13 12:06:42 UTC  
> Url: https://github.com/boostorg/system/pull/17#issuecomment-329147118  

It's not urgent, I just don't want this to be forgotten in develop. If the change is to be merged along with other changes before 1.66, it's fine by me.

---

## Comment 5

> Username: pdimov  
> Created_at: 2017-09-13 12:33:12 UTC  
> Url: https://github.com/boostorg/system/pull/17#issuecomment-329153069  

OK, I merged the changes up to and including this commit to master, leaving the more recent ones up to Beman.

---

## Comment 6

> Username: Lastique  
> Created_at: 2017-09-13 12:38:11 UTC  
> Url: https://github.com/boostorg/system/pull/17#issuecomment-329154230  

Thank you, Peter.

---

## Comment 7

> Username: Beman  
> Created_at: 2017-09-14 12:13:22 UTC  
> Url: https://github.com/boostorg/system/pull/17#issuecomment-329463210  

OK, merged.  
  
Thanks to both of you,  
  
--Beman

---
