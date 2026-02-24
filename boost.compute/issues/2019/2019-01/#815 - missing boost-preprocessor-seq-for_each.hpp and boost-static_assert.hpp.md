# #815 - missing boost/preprocessor/seq/for_each.hpp and boost/static_assert.hpp [Open]

> Username: mrbbfst  
> Created at: 2019-01-18 22:55:11 UTC  
> Updated at: 2019-02-08 18:42:49 UTC  
> Url: https://github.com/boostorg/compute/issues/815  

in accmulate.hpp   
  
`#include <boost/static_assert.hpp>  
#include <boost/preprocessor/seq/for_each.hpp>`  
  
there is no static _assert.hpp and preprocessor folder in the boost folder

---

## Comment 1

> Username: mrbbfst  
> Created at: 2019-01-18 23:21:15 UTC  
> Url: https://github.com/boostorg/compute/issues/815#issuecomment-455719050  

these files / folders and many others are missing

---

## Comment 2

> Username: HolyWu  
> Created at: 2019-01-18 23:55:55 UTC  
> Url: https://github.com/boostorg/compute/issues/815#issuecomment-455724746  

There is something wrong with your boost library installation, because those files/folders are indeed there. If in doubt, download from https://www.boost.org/users/download/ and check again.

---

## Comment 3

> Username: coderboyisongithub  
> Created at: 2019-02-08 09:29:07 UTC  
> Url: https://github.com/boostorg/compute/issues/815#issuecomment-461741920  

![image](https://user-images.githubusercontent.com/24197201/52469637-151f6f00-2bb4-11e9-87af-709d98b2fb4d.png)  
This is the file here...and this git ask for such header which dont exist in your respo...

---

## Comment 4

> Username: Ulfgard  
> Created at: 2019-02-08 09:32:03 UTC  
> Url: https://github.com/boostorg/compute/issues/815#issuecomment-461742754  

this is part of the full boost package. just download the newest version from boost.org

---

## Comment 5

> Username: coderboyisongithub  
> Created at: 2019-02-08 09:34:01 UTC  
> Url: https://github.com/boostorg/compute/issues/815#issuecomment-461743327  

This respo dont give full?

---

## Comment 6

> Username: Ulfgard  
> Created at: 2019-02-08 09:43:38 UTC  
> Url: https://github.com/boostorg/compute/issues/815#issuecomment-461746029  

this is the project repository of the  compute module of boost which depends on some other boost modules. only full releases contain everything.  
  
if you need some bug fix from the development of compute, you can replace the compute/ folder in a full boost installation with this repo.

---

## Comment 7

> Username: coderboyisongithub  
> Created at: 2019-02-08 18:20:37 UTC  
> Url: https://github.com/boostorg/compute/issues/815#issuecomment-461896961  

Will this version build on visual studio 2012..

---

## Comment 8

> Username: Ulfgard  
> Created at: 2019-02-08 18:42:49 UTC  
> Url: https://github.com/boostorg/compute/issues/815#issuecomment-461903590  

sorry, i don't do support. You have to try it out yourself.
