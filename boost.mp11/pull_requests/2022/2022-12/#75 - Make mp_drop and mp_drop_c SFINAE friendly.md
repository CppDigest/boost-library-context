# #75 Make mp_drop and mp_drop_c SFINAE friendly. [Merged]

> Username: jodyhagins  
> Created at: 2022-12-06 16:42:11 UTC  
> Updated at: 2022-12-07 19:12:49 UTC  
> Merged at: 2022-12-07 19:12:48 UTC  
> Closed at: 2022-12-07 19:12:48 UTC  
> Url: https://github.com/boostorg/mp11/pull/75  



---

## Comment 1

> Username: pdimov  
> Created_at: 2022-12-06 16:47:42 UTC  
> Url: https://github.com/boostorg/mp11/pull/75#issuecomment-1339662123  

Why not just check `N <= mp_size<L>` instead?

---

## Comment 2

> Username: jodyhagins  
> Created_at: 2022-12-06 16:51:07 UTC  
> Url: https://github.com/boostorg/mp11/pull/75#issuecomment-1339669793  

You mean before invoking it, instead of making it SFINAE friendly, or in the implementation?

---

## Comment 3

> Username: jodyhagins  
> Created_at: 2022-12-06 16:53:12 UTC  
> Url: https://github.com/boostorg/mp11/pull/75#issuecomment-1339672494  

The instantiation of the template itself has to be completely avoided, because the evaluation of R will generate a hard error.

---

## Comment 4

> Username: pdimov  
> Created_at: 2022-12-06 16:58:52 UTC  
> Url: https://github.com/boostorg/mp11/pull/75#issuecomment-1339680017  

I imagine something like  
```  
template<class L, std::size_t N> using mp_drop_c = typename detail::mp_drop_impl<L, mp_repeat_c<mp_list<void>, N>, mp_bool<N <= mp_size<L>::value>>::type;  
```  
where mp_drop_impl only has an implementation if the third parameter is `mp_true`.

---

## Comment 5

> Username: jodyhagins  
> Created_at: 2022-12-06 17:25:52 UTC  
> Url: https://github.com/boostorg/mp11/pull/75#issuecomment-1339722729  

OK.

---
