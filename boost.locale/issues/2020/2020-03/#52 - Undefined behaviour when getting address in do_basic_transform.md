# #52 - Undefined behaviour when getting address in do_basic_transform [Closed]

> Username: OznOg  
> Created at: 2020-03-08 07:55:20 UTC  
> Updated at: 2022-05-17 16:41:39 UTC  
> Closed at: 2022-05-17 16:41:39 UTC  
> Url: https://github.com/boostorg/locale/issues/52  

in  do_basic_transform https://github.com/boostorg/locale/blob/develop/src/icu/collator.cpp#L90  
line  
`  int len = collate->getSortKey(str,&tmp[0],tmp.size());`  
  
seems wrong as there is no way to make sure tmp is not a 0 sized vector which leads &tmp[0] to be undefined behaviour.  
Moreover, on my fedora 30, a vector assertion fails `__builtin_expect(__n < this->size(), true)` and leads the program to crash.  
  
A fix could be to use `tmp.data()` instead (if this function is really supposed to handle 0 lenght entry).

---

## Comment 1

> Username: OznOg  
> Created at: 2020-11-14 13:07:24 UTC  
> Url: https://github.com/boostorg/locale/issues/52#issuecomment-727204901  

a patch is proposed in https://github.com/boostorg/locale/pull/60

---

## Comment 2

> Username: OznOg  
> Created at: 2022-05-17 16:41:34 UTC  
> Url: https://github.com/boostorg/locale/issues/52#issuecomment-1129089221  

was fixed in https://github.com/boostorg/locale/pull/72
