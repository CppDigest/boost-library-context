# #92 - Suggestion: repeated transform [Open]

> Username: niekbouman  
> Created at: 2019-06-06 09:44:21 UTC  
> Updated at: 2019-06-15 22:10:39 UTC  
> Url: https://github.com/boostorg/yap/issues/92  

Dear Zach,  
  
How about adding a "repeated transform"? (See below, not sure if this is the best form, maybe some perfect forwarding is needed).  
Note that it requires the Comparable concept on Expr.  
  
best,  
Niek  
  
  
```cpp  
template <typename Expr, typename TF>  
constexpr auto repeated_transform(Expr &&expr, TF&& xform) {  
  // apply the transform repeatedly until the result no longer changes  
  
  return hana::while_(  
      [&](auto &&x) { return hana::not_equal(x, yap::transform(x, xform)); },  
      expr, [&](auto &&ex) { return yap::transform(ex, xform); });  
}  
```

---

## Comment 1

> Username: tzlaine  
> Created at: 2019-06-15 22:10:39 UTC  
> Url: https://github.com/boostorg/yap/issues/92#issuecomment-502403162  

That's not an unreasonable thing to want, but why isn't your implementation here enough?  That is, how does it help to have it as part of the library, rather than as a user-provided addition as you've done?
