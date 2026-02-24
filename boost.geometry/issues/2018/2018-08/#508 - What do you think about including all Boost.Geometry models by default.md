# #508 - What do you think about including all Boost.Geometry models by default? [Closed]

> Username: awulkiew  
> Created at: 2018-08-27 13:48:10 UTC  
> Updated at: 2022-10-19 13:30:25 UTC  
> Closed at: 2022-10-19 13:30:25 UTC  
> Url: https://github.com/boostorg/geometry/issues/508  

@barendgehrels @mloskot @vissarion What do you think about including all Boost.Geometry models by default in `geometry.hpp`? Internally we already use some of them anyway and the users are confused that some of them work and other doesn't.

---

## Comment 1

> Username: mloskot  
> Created at: 2018-08-27 16:52:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/508#issuecomment-416291507  

AFAIK/U, typically `#include <boost/xxx.hpp>` is supposed to import 100% definitions of Boost.XXX. Thus, I think, `#include <boost/geometry.hpp>` should import all models, indeed.

---

## Comment 2

> Username: vissarion  
> Created at: 2018-08-28 10:40:15 UTC  
> Url: https://github.com/boostorg/geometry/issues/508#issuecomment-416536600  

I agree with @mloskot

---

## Comment 3

> Username: barendgehrels  
> Created at: 2018-09-20 10:56:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/508#issuecomment-423139717  

I'm OK including them, but with some notes.  
  
Not including them was done on purpose. Boost.Geometry is a concept based library. It supports models fullfilling the rules of the concepts. So people can either use the models we provide, or use their own.  
  
Suppose they do NOT want to use the models we provide... Then they have additional compiletime, and maybe even other issues.  
  
There are also other headerfiles not included - should we include them too? It is not possible to include 100% because some definitions (mainly registration headers) are mutually exclusive...  
  
Anyway, if it is about the models for point, linestring, polygon, and multi-versions, I'm OK. If it is about point_xy I'm already a bit more sceptic. If it is about registration (for example of a boost array), I'm against.

---

## Comment 4

> Username: awulkiew  
> Created at: 2018-09-20 21:27:18 UTC  
> Url: https://github.com/boostorg/geometry/issues/508#issuecomment-423339232  

Indeed there is more headers in `geometries` dir. I was thinking about "regular" models defined in BG, nothing from `adapted` directory since these headers depend on other libraries and probably not from `register` but I'm not sure about that yet. And only if the user includes the main `geometry.hpp` header. `point_xy ` is heavily used in the examples so if we decided to include models I think it should be included too. I guess we could remove all instances of it from examples but it would be rather inconsistent to include all models besides one.  
  
I wouldn't care much about potential change in compilation time while including the main header. If the template is not instantiated then the user will not notice any additional compilation time. Parsing these few additional files WRT the rest of the library can be neglected.  
  
> Suppose they do NOT want to use the models we provide  
  
If they include the main header they include a lot more than they want to use anyway.  
  
> Then they have (...) maybe even other issues  
  
Do you have something specific in mind?  
  
> There are also other headerfiles not included - should we include them too  
  
Not if they depend on other libraries.  
  
> some definitions (mainly registration headers) are mutually exclusive  
  
What do you mean? That if a user includes a few of them the code will not compile? If that's the case then these headers are broken. Or does "mutually exclusive" mean something different?  
  
FYI, I'm not strongly for or against including models. However currently I see that on one hand it would be convenient for beginners and on the other I don't see any downsides in practice.

---

## Comment 5

> Username: mloskot  
> Created at: 2018-09-22 15:49:09 UTC  
> Updated at: 2018-09-22 15:49:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/508#issuecomment-423753229  

Perhaps, if core/non-extension models are better not included via the `geometry.hpp` or other 'all-in-one' headers, it could be clearly documented as Boost.MP11 does it  
  
![image](https://user-images.githubusercontent.com/80741/45919035-e8cab680-be8f-11e8-9c22-b77216d68224.png)  
  
(source https://www.boost.org/doc/libs/develop/libs/mp11/doc/html/mp11.html#mpl)

---

## Comment 6

> Username: barendgehrels  
> Created at: 2018-09-23 16:30:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/508#issuecomment-423828952  

I glanced through the sources and sometimes (not too often) we include them already. For example in the new discrete_frechet_distance.hpp. I think that should change (it looks not necessary anyway). But also in other sources, especially point / box but also sometimes the ring.  
  
So I'm OK including them by default, including point_xy if that wish is also expressed.  
  
But excluding other adaptations or registrations.
