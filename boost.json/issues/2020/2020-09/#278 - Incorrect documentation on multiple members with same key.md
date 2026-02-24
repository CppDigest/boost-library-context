# #278 - Incorrect documentation on multiple members with same key [Closed]

> Username: beached  
> Created at: 2020-09-05 20:41:33 UTC  
> Updated at: 2020-10-16 20:28:35 UTC  
> Closed at: 2020-10-16 20:28:34 UTC  
> Url: https://github.com/boostorg/json/issues/278  

The document http://develop.json.cpp.al/json/ref/boost__json__object/object/overload4.html says that the first is kept, but my understanding is the the last is kept

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-09-05 20:41:51 UTC  
> Url: https://github.com/boostorg/json/issues/278#issuecomment-687660375  

>If there are elements with duplicate keys, that is, if multiple elements in the range have keys that compare equal, only the last equivalent element will be inserted.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-09-05 20:42:48 UTC  
> Url: https://github.com/boostorg/json/issues/278#issuecomment-687660449  

parser and value_stack need similar treatment, and we should probably also mention it in the exposition, under "Using Objects"

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-10-16 20:28:34 UTC  
> Url: https://github.com/boostorg/json/issues/278#issuecomment-710571218  

This should be fixed now.
