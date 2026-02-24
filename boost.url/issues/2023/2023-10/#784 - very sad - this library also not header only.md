# #784 - very sad - this library also not header only [Closed]

> Username: fsmoke  
> Created at: 2023-10-25 09:01:57 UTC  
> Updated at: 2023-10-25 19:11:05 UTC  
> Closed at: 2023-10-25 17:09:12 UTC  
> Url: https://github.com/boostorg/url/issues/784  

Why again "boost" project getting various useful utility libraries, and again it's not the pure headeronly? - it's very very uncomfortable   
  
see my discussion json https://github.com/boostorg/json/issues/850  
  
and boost.url has same issue - even giant libraries such as boost.asio headeronly(dll, any, polygon, geometry, format, pfr, hana, fusion, function and another dozens of boost libraries also headeronly), but not json and url - why ? This way prevent to use it inside common headers in big projects - very uncomfortable

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-10-25 11:04:37 UTC  
> Url: https://github.com/boostorg/url/issues/784#issuecomment-1779021600  

Big projects don't do linking?

---

## Comment 2

> Username: fsmoke  
> Created at: 2023-10-25 11:40:37 UTC  
> Updated at: 2023-10-25 14:02:15 UTC  
> Url: https://github.com/boostorg/url/issues/784#issuecomment-1779079193  

> Big projects don't do linking?  
  
Big project - i mean project with hundreds of binaries(subprojects) in different source trees, different developers, different locations, and all of them use some common header from one common framework, this header pure header only. And if i include some inline function or class that uses url or json - i will be enforced to notify all developers(because build of all they subprojects will be broken due undefined references) - it's not good    
  
and now imagine - similar common utility headers - dozens in our framework  
  
my opinion: libraries like json and url - must be pure header only - there is many tasks parse tiny json string, or build url ....make light weight libraries like this link dependent - is absurd

---

## Comment 3

> Username: alandefreitas  
> Created at: 2023-10-25 17:09:13 UTC  
> Url: https://github.com/boostorg/url/issues/784#issuecomment-1779708393  

This discussion is out of scope. General complaints about boost can go to the boost mailing list.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2023-10-25 19:11:04 UTC  
> Url: https://github.com/boostorg/url/issues/784#issuecomment-1779890970  

> my opinion: libraries like json and url - must be pure header only  
  
Respectfully, I disagree. I am moving away from header-only for all my libraries because they cause increased compilation times and higher maintenance costs.
