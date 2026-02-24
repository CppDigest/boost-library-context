# #60 - Do we need to know explicitly the result of the adaptors? [Closed]

> Username: viboes  
> Created at: 2015-10-04 07:55:03 UTC  
> Updated at: 2015-10-14 16:03:24 UTC  
> Closed at: 2015-10-05 06:42:05 UTC  
> Url: https://github.com/boostorg/hof/issues/60  

I'm wondering if we need to name the result of the adaptors, or just say that it is an unspecified function with a specific signature that behaves as ....   
  
What is the advantage to giving it a name?   
  
Giving a name to the result type would imply to add the description of all of its operations.

---

## Comment 1

> Username: pfultz2  
> Created at: 2015-10-05 06:42:05 UTC  
> Url: https://github.com/boostorg/hof/issues/60#issuecomment-145443800  

Yes, because you can just use the class form of them. This is useful when you are just dealing with types. Also, the static function adaptors are all class-based so it makes it easier work with them as well.

---

## Comment 2

> Username: viboes  
> Created at: 2015-10-14 16:03:24 UTC  
> Url: https://github.com/boostorg/hof/issues/60#issuecomment-148099771  

Then you need to define more explicitly the interface of the adaptors, which operations are allowed, the semantic of each operation, ...
