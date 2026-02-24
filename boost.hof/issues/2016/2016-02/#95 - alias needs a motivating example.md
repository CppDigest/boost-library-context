# #95 - alias needs a motivating example [Closed]

> Username: tzlaine  
> Created at: 2016-02-10 18:01:14 UTC  
> Updated at: 2016-03-22 23:49:19 UTC  
> Closed at: 2016-03-22 23:49:19 UTC  
> Url: https://github.com/boostorg/hof/issues/95  

It's not at all clear why I'd use it, so it needs motivation or an example.  Otherwise, it should perhaps remain an implementation detail.

---

## Comment 1

> Username: pfultz2  
> Created at: 2016-02-20 22:42:21 UTC  
> Url: https://github.com/boostorg/hof/issues/95#issuecomment-186694047  

It is rather general-purpose. However, I do use this outside of the Fit library so I don't want to leave it as an implementation detail. Some examples where I use this:  
1. Tag elements with a key to build an associative sequence  
2. Tag the unit functions of a monad, so the monadic type can be deduced.   
  
Either one of these use cases don't lead to a simple motivating example, that is why I don't have a good example, yet.   
  
Plus, there are probably other examples of tagging types. I think I remember someone(maybe @viboes?) writing a `tagged` template for another library at some point that worked in a similar way.

---

## Comment 2

> Username: viboes  
> Created at: 2016-03-12 08:01:05 UTC  
> Url: https://github.com/boostorg/hof/issues/95#issuecomment-195693717  

Yes this is something that belong to a utility library.  
  
If not used internally by the library I believe that it should removed.

---

## Comment 3

> Username: pfultz2  
> Created at: 2016-03-12 17:14:17 UTC  
> Url: https://github.com/boostorg/hof/issues/95#issuecomment-195775966  

> Yes this is something that belong to a utility library.  
  
Well the Fit library is a utility library.  
  
> If not used internally by the library I believe that it should removed.  
  
It is used both internally and externally.
