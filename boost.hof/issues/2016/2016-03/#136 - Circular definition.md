# #136 - [BasicConcepts.Metafunction] Circular definition [Closed]

> Username: ldionne  
> Created at: 2016-03-11 20:12:36 UTC  
> Updated at: 2016-03-23 18:22:54 UTC  
> Closed at: 2016-03-22 23:54:35 UTC  
> Url: https://github.com/boostorg/hof/issues/136  

When defining the _Metafunction_ concept, it is written  
  
> Given  
> - `f`, a metafunction  
> - `args...`, any suitable type, which may be empty  
>   [...]  
  
However, I think defining `f` as a _metafunction_ is incorrect, because this is specifically the term we're trying to define. Of course this is clear if you already know what a metafunction is, but then there's no point in defining the concept :-).  
  
I would suggest saying something like   
  
> Given  
> - `f`, a type or a template  
  
which is more accurate as to the nature of `f`.

---

## Comment 1

> Username: ldionne  
> Created at: 2016-03-11 20:13:46 UTC  
> Url: https://github.com/boostorg/hof/issues/136#issuecomment-195527964  

The same comment applies to `MetafunctionClass` as well, except `f` should be defined as being a type and not a template.

---

## Comment 2

> Username: pfultz2  
> Created at: 2016-03-11 20:19:52 UTC  
> Url: https://github.com/boostorg/hof/issues/136#issuecomment-195530618  

I think this is a good idea, thanks.
