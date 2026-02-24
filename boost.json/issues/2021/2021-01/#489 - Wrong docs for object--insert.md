# #489 - Wrong docs for object::insert [Closed]

> Username: vinniefalco  
> Created at: 2021-01-25 00:38:15 UTC  
> Updated at: 2022-08-01 05:18:39 UTC  
> Closed at: 2022-08-01 05:18:39 UTC  
> Url: https://github.com/boostorg/json/issues/489  

The docs for `object::insert` says that it can replace but this is not correct. Only `insert_or_assign` replaces. See:  
  
https://www.boost.org/doc/libs/develop/libs/json/doc/html/json/ref/boost__json__object/insert/overload1.html
