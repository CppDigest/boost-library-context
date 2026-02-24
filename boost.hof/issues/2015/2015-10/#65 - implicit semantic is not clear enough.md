# #65 - implicit semantic is not clear enough [Closed]

> Username: viboes  
> Created at: 2015-10-04 13:23:14 UTC  
> Updated at: 2015-10-30 23:21:20 UTC  
> Closed at: 2015-10-30 23:21:20 UTC  
> Url: https://github.com/boostorg/hof/issues/65  

`implicit` is not an adaptor as the other as it is a class not a function.  
  
I would like to have in the documentation a more comprehensive description of how `implicit` behaves.   
  
Is something like that  
  
``` c++  
implicit<TC>(xs....).operator X() <==> TC<X>(xs...).operator X()  
```

---

## Comment 1

> Username: pfultz2  
> Created at: 2015-10-05 07:00:12 UTC  
> Url: https://github.com/boostorg/hof/issues/65#issuecomment-145445630  

I don't know a clean way to show it yet, since it relies on the implict conversion operator. I need to think about that.

---

## Comment 2

> Username: pfultz2  
> Created at: 2015-10-05 08:10:02 UTC  
> Url: https://github.com/boostorg/hof/issues/65#issuecomment-145459056  

> implicit is not an adaptor as the other as it is a class not a function.  
  
It is a StaticFunctionAdaptor as explained [here](http://fit.readthedocs.org/en/latest/overview/index.html#static-function-adaptor), so it is only a class(the `static_` adaptor is like this too). However, it takes a template instead of a class. Perhaps, I should move the static function adaptors to their own section.

---

## Comment 3

> Username: viboes  
> Created at: 2015-10-05 22:07:45 UTC  
> Url: https://github.com/boostorg/hof/issues/65#issuecomment-145683455  

As you have section for each king of Adaptors, Functions, maybe you can add directly Static Function Adaptors

---

## Comment 4

> Username: pfultz2  
> Created at: 2015-10-30 23:21:19 UTC  
> Url: https://github.com/boostorg/hof/issues/65#issuecomment-152672352  

Ok I added a semantic section to make this clearer.
