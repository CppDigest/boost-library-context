# #99 - Why is the ctor of optional<T&> explicit? [Open]

> Username: ImplOfAnImpl  
> Created at: 2021-11-24 11:22:27 UTC  
> Updated at: 2021-11-24 16:18:58 UTC  
> Url: https://github.com/boostorg/optional/issues/99  

I mean the ctor `template <class U> explicit optional(const optional<U&>& rhs)`. It doesn't seem to have much sense -  optional<T&> is logically a reference and it seems reasonable to allow it to be converted implicitly if the underlying pointer types allow this.  
With the current implementation the usage of optional references can be cumbersome, because even the non-const-ref to const-ref conversion has to be explicit.

---

## Comment 1

> Username: akrzemi1  
> Created at: 2021-11-24 11:49:26 UTC  
> Url: https://github.com/boostorg/optional/issues/99#issuecomment-977803067  

This is for consistency with the non-reference specialization: `optional<U>` is not implicitly convertible to `optional<T>` even if `U` is convertible to `T`.  
  
The missing `T`-to-`const T` can indeed be disturbing. This is also the case for non-references:  
  
```c++  
boost::optional<int> oi;  
boost::optional<const int> oci = oi;  
```  
 We could consider special casing the `T`-to-`const T` conversion.

---

## Comment 2

> Username: ImplOfAnImpl  
> Created at: 2021-11-24 14:42:47 UTC  
> Url: https://github.com/boostorg/optional/issues/99#issuecomment-977943629  

I see. But does this consistency actually make sense? I mean, for value types the implicit conversion can indeed do nasty stuff like slicing or narrowing, but converting references seems to always be benign.  
  
  
Btw, both kinds of `optional` have assignment operators corresponding to explicit ctors, which kind of defeats the purpose, because you can still perform an implicit conversion:  
```  
boost::optional<float> of;  
boost::optional<int> oi;  
oi = of;  
```  
So it might make sense to make `boost::optional<T>` behave like `std::optional` in this regard, i.e. the converting ctor is implicit if the underlying types are implicitly convertible to each other (and the assignment is present only of the underlying types are assignable). In this case making ctors of `boost::optional<T&>` implicit will actually make it consistent with `boost::optional<T>`.

---

## Comment 3

> Username: akrzemi1  
> Created at: 2021-11-24 15:12:02 UTC  
> Updated at: 2021-11-24 15:22:26 UTC  
> Url: https://github.com/boostorg/optional/issues/99#issuecomment-977969224  

Unless the binding of temporaries to lvalue references to `const` is explicitly disabled., even the reference casts have the same problems:  
  
```c++  
  int i = -1;  
  unsigned const& ui = i;  
```  
  
Consistency with `std::optional` is not necessarily a good thing, as `std::optional` has design flaws that cause ambiguities:  
  
```c++  
template <typename T, typename U>  
void test_optional()  
{  
  std::optional<U> ou = std::nullopt;  
  std::optional<T> ot = ou;  
  assert (ot == std::nullopt); // true for some Ts and Us, false for other Ts and Us.  
}  
```   
  
The above looks like an obvious thing to expect, but fails for  
  
```c++  
test_optional<std::optional<int>, int>();  
```  
  
`boost::optional` has fewer gotchas like the one above, because it does not try to be 100% consistent with `std::optional`.

---

## Comment 4

> Username: ImplOfAnImpl  
> Created at: 2021-11-24 15:19:02 UTC  
> Url: https://github.com/boostorg/optional/issues/99#issuecomment-977975270  

> Unless the binding of temporaries to lvalue references to const is explicitly disabled. Even the reference casts have the same problems  
  
Yes, I meant converting lvalue references of course (there is no boost::optional<T&&> anyway).  
  
> Consistency with std::optional is not necessarily a good thing  
  
I see.

---

## Comment 5

> Username: akrzemi1  
> Created at: 2021-11-24 15:24:44 UTC  
> Url: https://github.com/boostorg/optional/issues/99#issuecomment-977980299  

Would allowing a conversion from `T` to `const T` address your issue?  
Or conversion from `Derived&` to `Base&`?

---

## Comment 6

> Username: ImplOfAnImpl  
> Created at: 2021-11-24 16:18:58 UTC  
> Url: https://github.com/boostorg/optional/issues/99#issuecomment-978027642  

Well, the absence of the `T&` to `const T&` conversion is what caused me to report the issue.  
But IMO the conversion from `Derived&` to `Base&` would also be nice to have.
