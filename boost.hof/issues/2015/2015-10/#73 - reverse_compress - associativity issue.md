# #73 - reverse_compress - associativity issue [Closed]

> Username: viboes  
> Created at: 2015-10-04 16:56:20 UTC  
> Updated at: 2015-10-14 20:22:28 UTC  
> Closed at: 2015-10-05 07:15:47 UTC  
> Url: https://github.com/boostorg/hof/issues/73  

right fold is right associative and the function has the signature `f:: A  B -> B` while left fold is left associative and the function has signature `f:: A  B -> A`. A and B are the same and the function is commutative there is no difference, but I believe the need for the difference is to take care of this non-commutative cases.  
  
```  
foldr f z []     = z  
foldr f z (x:xs) = x `f` foldr f z xs  
  
foldl f z []     = z  
foldl f z (x:xs) = let z' = z `f` x   
                   in foldl f z' xs  
```  
  
See https://wiki.haskell.org/Foldr_Foldl_Foldl' for more details.  
  
`````` c++  
assert(reverse_compress(f, z)(x, xs...) == f(reverse_compress(f, z)(xs...), x));  
`  
  
should be  
```c++  
assert(reverse_compress(f, z)(x, xs...) == f(x, reverse_compress(f, z)(xs...)));  
``````

---

## Comment 1

> Username: pfultz2  
> Created at: 2015-10-05 07:15:47 UTC  
> Url: https://github.com/boostorg/hof/issues/73#issuecomment-145447309  

Its called reverse, so that the same binary operator will happen in reverse with `reverse_compress`, that is:  
  
``` cpp  
assert(fit::compress(_+_, std::string())("Hello", "-", "World") == "Hello-World");  
assert(fit::reverse_compress(_+_, std::string())("Hello", "-", "World") == "World-Hello");  
```  
  
If I used right associativity as you suggest, then they both would give the same result, which is not desired.

---

## Comment 2

> Username: viboes  
> Created at: 2015-10-14 15:57:31 UTC  
> Url: https://github.com/boostorg/hof/issues/73#issuecomment-148097781  

Does this mean that we need an additional `compress_right`?

---

## Comment 3

> Username: pfultz2  
> Created at: 2015-10-14 20:22:28 UTC  
> Url: https://github.com/boostorg/hof/issues/73#issuecomment-148184183  

> Does this mean that we need an additional compress_right?  
  
For what?
