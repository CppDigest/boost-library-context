# #68 - [Functional] Find a better name for reverse_partial [Closed]

> Username: ldionne  
> Created at: 2015-05-04 17:19:06 UTC  
> Updated at: 2015-10-06 14:01:57 UTC  
> Closed at: 2015-10-06 14:01:57 UTC  
> Url: https://github.com/boostorg/hana/issues/68  

`reverse_partial` is equivalent to `partial`, except it binds the arguments at the front instead of the back. In other words,  
  
``` cpp  
reverse_partial(f, args...)(x...) == f(x..., args...)  
```  
  
This is also somewhat equivalent to `capture`, except for the way it is called:  
  
``` cpp  
capture(x...)(f)(args...) == f(x..., args...)  
```

---

## Comment 1

> Username: ldionne  
> Created at: 2015-10-06 14:01:57 UTC  
> Url: https://github.com/boostorg/hana/issues/68#issuecomment-145865037  

That name isn't that bad, really. Keeping it that way.
