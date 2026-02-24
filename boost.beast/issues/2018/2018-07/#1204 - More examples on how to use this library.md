# #1204 - More examples on how to use this library [Closed]

> Username: dk1013  
> Created at: 2018-07-23 06:19:06 UTC  
> Updated at: 2018-07-23 14:28:24 UTC  
> Closed at: 2018-07-23 14:28:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1204  

I would like to thank @vinniefalco for writing this useful library.  
However, as a newbie to asio and beast, I find it quite confusing to understand the typical patterns to use this library. To be more specific, I think more examples to prepare HTTP requests of various scenarios will be very helpful. For example, how do I add customizable fields to the header?  
Sorry for raising this if this sounds too simple for the experienced users.  
Thanks!

---

## Comment 1

> Username: djarek  
> Created at: 2018-07-23 11:22:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1204#issuecomment-407024433  

> For example, how do I add customizable fields to the header  
  
Could you be more clear - do you want to write your own Fields class or do you want to add HTTP fields (e.g. `X-Csrf-Token: i8XNjC4b8KVok4uw5RftR38Wgp2BFwql`)? the latter is fairly easy and should already be explained in the docs.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-07-23 13:08:46 UTC  
> Updated at: 2018-07-23 13:09:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1204#issuecomment-407051877  

`http::message`  
_"This container is derived from the Fields template type. To understand all of the members of this class it is necessary to view the declaration for the Fields type. When using the default fields container, those declarations are in http::fields."_  
https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/ref/boost__beast__http__message.html  
  
`http::fields`  
https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/ref/boost__beast__http__fields.html  
  
`fields::insert`  
https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/ref/boost__beast__http__basic_fields/insert.html  
  
`fields::set`  
https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/ref/boost__beast__http__basic_fields/set.html

---

## Comment 3

> Username: dk1013  
> Created at: 2018-07-23 14:27:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1204#issuecomment-407077044  

Hi @djarek and @vinniefalco , thanks a lot for the prompt reply! Your comments and refs are very useful. Will read through the documentation more carefully. You may want to close this issue for now. Thanks again for the support!  Regards, Kai
