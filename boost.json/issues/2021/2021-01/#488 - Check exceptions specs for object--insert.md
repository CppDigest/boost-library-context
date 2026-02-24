# #488 - Check exceptions specs for object::insert [Closed]

> Username: vinniefalco  
> Created at: 2021-01-25 00:21:34 UTC  
> Updated at: 2024-04-08 15:04:39 UTC  
> Closed at: 2024-04-08 15:04:39 UTC  
> Url: https://github.com/boostorg/json/issues/488  

Should both of the listed conditions throw `length_error`?  
https://www.boost.org/doc/libs/develop/libs/json/doc/html/json/ref/boost__json__object/insert/overload1.html

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-04-08 15:04:39 UTC  
> Url: https://github.com/boostorg/json/issues/488#issuecomment-2043004033  

As of be759c5051b828f77b59d5c67b9b53d7330e627e it throws `system_error` and this is mentioned in the docs.
