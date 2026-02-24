# #32 - The use of FIT_STATIC_LAMBDA_FUNCTION for match/conditional constraints the implementation [Closed]

> Username: viboes  
> Created at: 2015-10-04 01:15:56 UTC  
> Updated at: 2015-10-05 07:54:20 UTC  
> Closed at: 2015-10-05 04:29:24 UTC  
> Url: https://github.com/boostorg/hof/issues/32  

IIUC match and conditional return today a lambda, but they could as well return a function object in the future, isn't it?  
  
I will replace the macro `FIT_STATIC_LAMBDA_FUNCTION` by a more specific one,e.g. FIT_STATIC_CONDITIONAL_FUNCTION.   
  
``` c++  
FIT_STATIC_LAMBDA_FUNCTION(print) = conditional( ...);  
The same for match.  
  
```

---

## Comment 1

> Username: pfultz2  
> Created at: 2015-10-05 04:29:24 UTC  
> Url: https://github.com/boostorg/hof/issues/32#issuecomment-145427349  

The `FIT_STATIC_LAMBDA_FUNCTION` is used because lambdas are passed to `fit::conditional`, but if function objects were passed then `FIT_STATIC_FUNCTION` can be used instead.

---

## Comment 2

> Username: viboes  
> Created at: 2015-10-05 05:49:17 UTC  
> Url: https://github.com/boostorg/hof/issues/32#issuecomment-145436752  

Do you mean that we can use both? If yes, this is not clear in the documentation.

---

## Comment 3

> Username: pfultz2  
> Created at: 2015-10-05 07:54:20 UTC  
> Url: https://github.com/boostorg/hof/issues/32#issuecomment-145455108  

I don't understand why that isn't clear as the examples for both `conditional` and `match` use function objects. Also, they are not related at all to the `FIT_STATIC_*` macros, and the documentation makes no reference to them either.
