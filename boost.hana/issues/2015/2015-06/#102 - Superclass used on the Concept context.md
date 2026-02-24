# #102 - [Documentation] Superclass used on the Concept context [Closed]

> Username: viboes  
> Created at: 2015-06-13 13:50:23 UTC  
> Updated at: 2015-06-14 12:18:51 UTC  
> Closed at: 2015-06-13 19:26:53 UTC  
> Url: https://github.com/boostorg/hana/issues/102  

The use of Superclass on the context of the type class definitions is weird. A type class (Concept) is not really a class. The relation of sub-class super-class is not defined.   
  
I suggest to use type class instead of concept, and define the relation of sub-type-class and super-type-class.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-06-13 19:12:26 UTC  
> Url: https://github.com/boostorg/hana/issues/102#issuecomment-111741435  

I agree that using superclass is misleading. However, like I said in #97, I don't want to use "Type class" and related terms. I'll use the notion of "Refinement", which is the proper term for what I want to express and [has been used](https://www.sgi.com/tech/stl/stl_introduction.html) in C++ since the SGI STL.

---

## Comment 2

> Username: viboes  
> Created at: 2015-06-14 12:05:43 UTC  
> Url: https://github.com/boostorg/hana/issues/102#issuecomment-111817146  

I agree refinement is much better in some cases, but we have more than refinements as I've described in #104. We have just concrete models if you want, subject to some conditions.

---

## Comment 3

> Username: ldionne  
> Created at: 2015-06-14 12:14:10 UTC  
> Url: https://github.com/boostorg/hana/issues/102#issuecomment-111820444  

I understand. These would be much better described by "Requirements". Actually, the superclasses are sort of part of the minimal complete definition, in some sense.

---

## Comment 4

> Username: ldionne  
> Created at: 2015-06-14 12:18:51 UTC  
> Url: https://github.com/boostorg/hana/issues/102#issuecomment-111821642  

However, this issue of expressing  
  
``` haskell  
Monoid a => Monoid (Maybe a)  
```  
  
is not really present in Hana, because it does not use parameterized tags. When a similar situation arises, I just describe it in words instead of having a notation for it, or a unifying notion of "requirements".
