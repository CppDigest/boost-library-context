# #71 fixes #70: remove un-necessary constructors from expression [Closed]

> Username: rgrover  
> Created at: 2018-03-09 18:57:30 UTC  
> Updated at: 2018-06-11 17:16:20 UTC  
> Closed at: 2018-03-10 01:45:10 UTC  
> Url: https://github.com/boostorg/yap/pull/71  



---

## Comment 1

> Username: tzlaine  
> Created_at: 2018-03-10 01:45:10 UTC  
> Url: https://github.com/boostorg/yap/pull/71#issuecomment-371992200  

This is unnecessary for what we really want -- to make expression<> constexpr.  This has latter change just been committed.

---

## Comment 2

> Username: rgrover  
> Created_at: 2018-03-10 02:12:00 UTC  
> Url: https://github.com/boostorg/yap/pull/71#issuecomment-371994278  

What I would really like to have is the ability to initialize an expression like an aggregate. With the help of a deduction guide, such as  
```C++  
template <typename T>  
expression(T)->expression<expr_kind::terminal, boost::hana::tuple<T>>;  
```  
  
I can then write `expression{1}`

---

## Comment 3

> Username: tzlaine  
> Created_at: 2018-03-10 04:11:39 UTC  
> Url: https://github.com/boostorg/yap/pull/71#issuecomment-372001057  

CTAD works with ctors.  Define one for expression<> if you like, and construct expression<>s with "expression(1)".  Does that not work for some reason?

---

## Comment 4

> Username: rgrover  
> Created_at: 2018-03-10 05:13:31 UTC  
> Url: https://github.com/boostorg/yap/pull/71#issuecomment-372003826  

yes, CTAD works with constructors. I essentially want constexpr expression. thanks.

---
