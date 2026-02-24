# #33 - Proposal mp_eval_and? [Closed]

> Username: HDembinski  
> Created at: 2019-05-13 20:00:09 UTC  
> Updated at: 2019-06-14 18:15:18 UTC  
> Closed at: 2019-06-14 18:15:18 UTC  
> Url: https://github.com/boostorg/mp11/issues/33  

This is a combination of `mp_eval_if` and `mp_and`. `mp_and` already shortcuts on the first substitution failure when `mp_to_bool` is applied to `T` or when the application yields `mp_false`. `mp_eval_and` would provide the same functionality for applications of meta functions `F...` on `T`.  
```  
template <class T, template<class>class F...> using mp_eval_and = ...  
```  
`mp_eval_and<T, F0, F1, ...>` is `mp_false`, if `F0<T>` is `mp_false` or a substitution failure. Otherwise it is `mp_eval_and<T, F1, ...>` and so on.  
  
Example application for `mp_eval_and`  
```  
template <class T>  
using is_unsigned_integral = mp_eval_and<T, std::is_integral, std::is_unsigned>;  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2019-05-13 20:33:25 UTC  
> Url: https://github.com/boostorg/mp11/issues/33#issuecomment-491975451  

Is this the one-liner  
```  
template<class T, template<class...> class... F> using mp_eval_and = mp_and<F<T>...>;  
```  
?

---

## Comment 2

> Username: HDembinski  
> Created at: 2019-05-13 20:48:51 UTC  
> Url: https://github.com/boostorg/mp11/issues/33#issuecomment-491980633  

I just tried it out, the one-liner seems to work, although I don't get why. I thought the right hand side `mp_and<F<T>>` would cause a substitution failure if `F<T>` is a substitution failure. `mp_eval_and` should never cause a substitution failure.

---

## Comment 3

> Username: HDembinski  
> Created at: 2019-05-13 20:51:42 UTC  
> Url: https://github.com/boostorg/mp11/issues/33#issuecomment-491981666  

I have an implementation here: https://github.com/HDembinski/mp11/tree/mp_eval_and, pieced together from looking at how you do things.

---

## Comment 4

> Username: pdimov  
> Created at: 2019-05-13 21:10:43 UTC  
> Url: https://github.com/boostorg/mp11/issues/33#issuecomment-491988215  

In your example, `F0` is `is_integral`, and `is_integral<T>` is never a substitution failure, because it's a struct. `F0<T>::value` can be a substitution failure, but it's eaten by `mp_and`.

---

## Comment 5

> Username: HDembinski  
> Created at: 2019-05-14 08:37:49 UTC  
> Url: https://github.com/boostorg/mp11/issues/33#issuecomment-492144100  

Ok, then my example is just bad and my understanding of your one-liner was correct at least.

---

## Comment 6

> Username: HDembinski  
> Created at: 2019-06-14 18:15:18 UTC  
> Url: https://github.com/boostorg/mp11/issues/33#issuecomment-502211847  

I am closing this as it seems superfluous.
