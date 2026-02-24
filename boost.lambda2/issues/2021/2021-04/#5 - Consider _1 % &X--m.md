# #5 - Consider _1 % &X::m [Closed]

> Username: pdimov  
> Created at: 2021-04-14 18:45:56 UTC  
> Updated at: 2021-10-18 18:34:19 UTC  
> Closed at: 2021-10-18 18:34:19 UTC  
> Url: https://github.com/boostorg/lambda2/issues/5  

E.g. https://godbolt.org/z/9cfYqj3j7

---

## Comment 1

> Username: huangqinjin  
> Created at: 2021-05-27 10:37:20 UTC  
> Url: https://github.com/boostorg/lambda2/issues/5#issuecomment-849526585  

Isn't `_1 ->* &X::m` more expressive?

---

## Comment 2

> Username: pdimov  
> Created at: 2021-06-27 16:35:03 UTC  
> Url: https://github.com/boostorg/lambda2/issues/5#issuecomment-869190778  

`->*` works too; I'll think about it some more. https://godbolt.org/z/4x8PMTGWs

---

## Comment 3

> Username: pdimov  
> Created at: 2021-06-27 16:36:55 UTC  
> Url: https://github.com/boostorg/lambda2/issues/5#issuecomment-869191069  

`first`/`second` specifically can also be made members of `_1`, so that the more straightforward `_1.first` and `_1.second` works, e.g. https://godbolt.org/z/d8ejbxMfP. But `_1->*first` is user-extensible to other identifiers, whereas `_1.first` is not.

---

## Comment 4

> Username: beojan  
> Created at: 2021-07-13 10:30:43 UTC  
> Updated at: 2021-07-13 10:33:29 UTC  
> Url: https://github.com/boostorg/lambda2/issues/5#issuecomment-878972934  

Wouldn't it need to be `_1->*&std::pair<T1, T2>::first`?  
  
Would it be possible to provide free functions like `get` (maybe even both the index and type (used by `std::variant`) versions) and `at` (forwarding to the `at` member function)? Then the macros used for this could be used to provide other functions like those in `cmath` and the user could define "lifted" (stealing some terminology from Boost::HOF) versions of any function they like.  
  
That said, I definitely prefer `->*` over `%` since I can see lambdas like `_1 % 2 == 0` not being uncommon.
