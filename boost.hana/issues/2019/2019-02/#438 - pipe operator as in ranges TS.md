# #438 - pipe operator as in ranges TS [Open]

> Username: godefv  
> Created at: 2019-02-08 22:30:07 UTC  
> Updated at: 2019-02-15 20:55:25 UTC  
> Url: https://github.com/boostorg/hana/issues/438  

It would be nice to have the same syntax as the ranges TS.  
  
For example, instead of   
```  
boost::hana::fold(  
  boost::hana::transform(  
    boost::hana::values(map)  
  ,func)  
,boost::hana::union_)  
```  
I would write  
```  
map|boost::hana::values  
   |boost::hana::transform(func)  
   |boost::hana::fold(boost::hana::union_)  
```

---

## Comment 1

> Username: ricejasonf  
> Created at: 2019-02-08 23:05:01 UTC  
> Url: https://github.com/boostorg/hana/issues/438#issuecomment-461977309  

Checkout [`hana::tuple`](http://boostorg.github.io/hana/structboost_1_1hana_1_1tuple.html#a59b4d21f17e2caed8c31d8389d0f0878), [`hana::lazy`](http://boostorg.github.io/hana/structboost_1_1hana_1_1lazy.html#a97b267b09ee075f1c531b849969e75e6), or [`hana::optional`](http://boostorg.github.io/hana/structboost_1_1hana_1_1optional.html#a833b9d5d44bb431621af9129994b9bbc)  
Here the `operator|` is typically used with `hana::Monad`s  
  
For point-free programming check out Boost.Hof

---

## Comment 2

> Username: godefv  
> Created at: 2019-02-13 13:17:07 UTC  
> Url: https://github.com/boostorg/hana/issues/438#issuecomment-463195374  

ok, I didn't know about that.  
It seems that the idea of the operator| for monads is indeed chaining functions, but it doesn't work to chain transforms, folds, etc, right ?

---

## Comment 3

> Username: godefv  
> Created at: 2019-02-15 09:06:27 UTC  
> Updated at: 2019-02-15 09:08:15 UTC  
> Url: https://github.com/boostorg/hana/issues/438#issuecomment-463961520  

I have tried to used `boost::hana::operator|` like this :   
```  
map|boost::hana::values  
   |[](auto const& x){return boost::hana::transform(x, func);}  
   |[](auto const& x){return boost::hana::fold(x, boost::hana::union_);}  
```  
The compiler complains that transform requires a functor as x.  
  
So, I have defined myself `auto operator|(T&&, Invokable<T>&&)`.   
Without the `Invokable` constraint, it conflicts with `boost::hana::operator|`, but with it, it seems to work.  
  
It would be nicer to be able to use `boost::hana::transform(boost::hana::_, func)`. Is it intended that this syntax is not allowed ? EDIT : actually, I have not tried it again with my own operator, it might work...

---

## Comment 4

> Username: ricejasonf  
> Created at: 2019-02-15 20:55:25 UTC  
> Url: https://github.com/boostorg/hana/issues/438#issuecomment-464195924  

The placeholder `_` is just an object that supports some operators. Boost.Hof is probably what you are looking for if you want high order functions.
