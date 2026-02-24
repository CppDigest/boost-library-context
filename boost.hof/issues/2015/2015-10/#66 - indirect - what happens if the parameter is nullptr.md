# #66 - indirect - what happens if the parameter is nullptr [Closed]

> Username: viboes  
> Created at: 2015-10-04 13:38:27 UTC  
> Updated at: 2016-03-07 00:11:55 UTC  
> Closed at: 2015-10-30 23:15:23 UTC  
> Url: https://github.com/boostorg/hof/issues/66  

``` c++  
unique_ptr<sum> psum;  
int r = indirect(psum)(3,2);  
```  
  
It seems that there is a precondition missing. It should be more explicit the operation used on the parameter, in this case `operator*()` I guess.  
  
`Applicatives` have a similar and safer function that returns another Applicative having the same type-constructor.  
  
```  
(<*>) :: f (a -> b) -> f a -> f b  
```  
  
if f is also a `Monad` `<*>` is equal to `ap`  
  
```  
auto r = ap(psum, make_unique(3,2)); // decltype x is unique_ptr<int>  
```  
  
As defined, the parameter should be a model of a `PossiblyNull` or `PossyblyValued` concept supporting the dereference function.

---

## Comment 1

> Username: pfultz2  
> Created at: 2015-10-05 07:05:29 UTC  
> Url: https://github.com/boostorg/hof/issues/66#issuecomment-145446195  

It is undefined behavior to dereference null.  
  
> As defined, the parameter should be a model of a PossiblyNull  
  
Dereferencibility is only required, since, you can pass a Dereferenceable object that is never null. That is being able to dereference an object is unrelated to whether it can be null or not, and `fit::indirect` doesn't ever check for something null.

---

## Comment 2

> Username: viboes  
> Created at: 2015-10-05 22:05:42 UTC  
> Url: https://github.com/boostorg/hof/issues/66#issuecomment-145682791  

Wondering if some clarifications can added to help the user to understand that this case is UB.

---

## Comment 3

> Username: pfultz2  
> Created at: 2015-10-30 23:15:23 UTC  
> Url: https://github.com/boostorg/hof/issues/66#issuecomment-152671678  

I added a semantic section to make this clearer.
