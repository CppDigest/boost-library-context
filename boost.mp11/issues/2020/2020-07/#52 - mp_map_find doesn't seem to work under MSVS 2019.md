# #52 - mp_map_find doesn't seem to work under MSVS 2019 [Closed]

> Username: geluspeculum  
> Created at: 2020-07-21 15:23:05 UTC  
> Updated at: 2023-03-24 11:58:41 UTC  
> Closed at: 2023-03-24 11:58:41 UTC  
> Url: https://github.com/boostorg/mp11/issues/52  

Hi!  
  
I'm using `mp_map_find` variant in our code and recently my Windows-using colleagues reported that it stopped working under MSVS 2019.  
I've tried this: https://godbolt.org/z/nnPWa5 and indeed it doesn't seem to work.  
  
I wonder, maybe there is a known workaround or at least a known MSVC issue?  
  
It looks like MSVC has troubles with template template parameter of `f` helper function.

---

## Comment 1

> Username: pdimov  
> Created at: 2020-07-21 16:02:32 UTC  
> Url: https://github.com/boostorg/mp11/issues/52#issuecomment-661950073  

That's pretty horrible.  
  
The problem seems to be caused by `P` being `std::tuple`. Using `std::pair` or `mp_list` there is fine. There's something in the `tuple` implementation that confuses MSVC. I'll try to figure out what.  
  
Thanks for the test case.

---

## Comment 2

> Username: pdimov  
> Created at: 2020-07-21 16:38:17 UTC  
> Url: https://github.com/boostorg/mp11/issues/52#issuecomment-661970742  

`tuple` is approximately  
```  
template<class...> class tuple;  
template<> class tuple<> {};  
template<class Head, class... Tail> class tuple<Head, Tail...>: private tuple<Tail...> {};  
```  
under MSVC, and this seems to confuse the overload resolution process somehow.  
  
I can work around this by temporarily replacing `std::tuple` in `M` with something else, then replacing it back in the result. This is not ideal, as it won't apply to user-defined tuple types like the above one, but nothing better comes to mind.  
  
On your side, it's probably best to just not use `std::tuple` as `P`.  
  
Please consider reporting this as a bug to MS, so that they have a chance to also fix it on their side.

---

## Comment 3

> Username: geluspeculum  
> Created at: 2020-07-23 10:34:28 UTC  
> Url: https://github.com/boostorg/mp11/issues/52#issuecomment-662934486  

Thanks!  
  
https://developercommunity.visualstudio.com/content/problem/1122007/template-parameter-pattern-matching-failure-in-a-c.html

---

## Comment 4

> Username: pdimov  
> Created at: 2020-09-25 17:04:51 UTC  
> Url: https://github.com/boostorg/mp11/issues/52#issuecomment-699044231  

Reopening until the MS issue is fixed.
