# #148 - Error when combining lazy adaptor with match [Closed]

> Username: pfultz2  
> Created at: 2016-03-15 17:13:21 UTC  
> Updated at: 2016-03-23 18:17:12 UTC  
> Closed at: 2016-03-22 23:57:51 UTC  
> Url: https://github.com/boostorg/hof/issues/148  

This snippet doesn't compile, but should:  
  
``` cpp  
const auto negate = (!_1);  
const auto sub = (_1 - _2);  
std::cout << fit::match(negate, sub)(0, 1) << std::endl;  
```

---

## Comment 1

> Username: pfultz2  
> Created at: 2016-03-16 18:14:12 UTC  
> Url: https://github.com/boostorg/hof/issues/148#issuecomment-197464856  

Actually, this will never work. This is because the lazy adaptor masks the arity of the function and there is no way for the compiler to pick the better match.

---

## Comment 2

> Username: pfultz2  
> Created at: 2016-03-16 18:30:35 UTC  
> Url: https://github.com/boostorg/hof/issues/148#issuecomment-197474985  

Nervermind, this is a bug due to the fact that `arg` is not SFINAE-friendly.
