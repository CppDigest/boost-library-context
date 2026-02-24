# #237 Fixed reverse_view [Merged]

> Username: denzor200  
> Created at: 2021-11-06 23:03:43 UTC  
> Updated at: 2022-01-16 10:10:43 UTC  
> Merged at: 2022-01-16 10:10:43 UTC  
> Closed at: 2022-01-16 10:10:43 UTC  
> Url: https://github.com/boostorg/fusion/pull/237  

The problem in question is "impossibility to apply any associative functions to `reverse_view`".  
I keep getting errors like this when I try, for example, to apply `at_key` over `reverse_view`:  
`/opt/compiler-explorer/libs/boost_1_77_0/boost/fusion/view/reverse_view/detail/key_of_impl.hpp:23:16: error: no type named 'it_type'`  
  
More detailed example here:  
https://godbolt.org/z/evqv4z9an

---

## Comment 1

> Username: djowel  
> Created_at: 2021-11-07 01:51:29 UTC  
> Url: https://github.com/boostorg/fusion/pull/237#issuecomment-962538151  

Could you offer a description of this "issue"?

---

## Comment 2

> Username: denzor200  
> Created_at: 2021-11-07 15:50:04 UTC  
> Url: https://github.com/boostorg/fusion/pull/237#issuecomment-962634359  

@djowel I am sorry for the misunderstanding!😃  
I forgot to create an issue. Description updated

---

## Comment 3

> Username: denzor200  
> Created_at: 2021-11-07 23:30:54 UTC  
> Url: https://github.com/boostorg/fusion/pull/237#issuecomment-962702388  

@djowel the problem was fixed. Ready for review

---
