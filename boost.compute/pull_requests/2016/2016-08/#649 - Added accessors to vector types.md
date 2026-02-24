# #649 Added accessors to vector types [Closed]

> Username: amirshavit  
> Created at: 2016-08-23 12:52:32 UTC  
> Updated at: 2019-02-10 15:30:46 UTC  
> Closed at: 2019-02-10 15:30:45 UTC  
> Url: https://github.com/boostorg/compute/pull/649  

This basically replaces the protected m_values member with public x/y, x/y/z/w, s0..s7, s0..sf members according to the vector size.

---

## Comment 1

> Username: jszuppe  
> Created_at: 2016-08-24 09:32:30 UTC  
> Url: https://github.com/boostorg/compute/pull/649#issuecomment-242007889  

Is this approach safe? I mean, if I recall correctly a compiler can introduce a padding between two member variables. Of course I don't think that would happen in that case when using any popular C++ compiler for x86 like g++ or clang++, but it's still a question.

---

## Comment 2

> Username: amirshavit  
> Created_at: 2016-08-24 10:01:42 UTC  
> Url: https://github.com/boostorg/compute/pull/649#issuecomment-242014654  

I don't think it's less safe than an array of scalars, but I'll try to find some text to support that.  
Either way, a static assert of `sizeof(Scalar) * N == sizeof(vector_type<Scalar, N>)` can remove all concerns.

---

## Comment 3

> Username: jszuppe  
> Created_at: 2016-08-24 10:07:22 UTC  
> Url: https://github.com/boostorg/compute/pull/649#issuecomment-242015954  

> I don't think it's less safe than an array of scalars, but I'll try to find some text to support that.  
  
I am positive that a compiler can't put padding into an array of scalars.  
  
> Either way, a static assert of sizeof(Scalar) \* N == sizeof(vector_type<Scalar, N>) can remove all concerns.  
  
:+1:

---

## Comment 4

> Username: coveralls  
> Created_at: 2017-03-25 14:04:57 UTC  
> Url: https://github.com/boostorg/compute/pull/649#issuecomment-289213727  

[![Coverage Status](https://coveralls.io/builds/10770846/badge)](https://coveralls.io/builds/10770846)  
  
Coverage increased (+0.07%) to 83.404% when pulling **d824ead3b88c754e9a99f95769936a03350a7dda on amirshavit:accessors** into **82f588f24ed2640bca4bf3f31dfcb06703d9bc13 on boostorg:develop**.

---

## Comment 5

> Username: jszuppe  
> Created_at: 2019-02-10 15:30:45 UTC  
> Url: https://github.com/boostorg/compute/pull/649#issuecomment-462142120  

Done in #823

---
