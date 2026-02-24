# #55 - Define std::hash<boost::optional<T>> for compat with std::optional [Closed]

> Username: alexweej  
> Created at: 2018-07-04 12:25:46 UTC  
> Updated at: 2022-09-06 23:04:19 UTC  
> Closed at: 2022-09-06 23:04:19 UTC  
> Url: https://github.com/boostorg/optional/issues/55  

[Edit] - title was updated, see comment 2 below:  
  
Currently `boost::optional<T>` are not hashable, but `std::optional<T>` are.  
  
`boost::variant<Ts...>` on the other hand *is* hashable. It seems fair that `boost::optional<T>` should have a `hash_value` ADL method.  
  
Impl is fairly trivial, but it needs to be defined inside the boost namespace obviously so better to do it in Boost than in user code.  
  
https://github.com/gcc-mirror/gcc/blob/edf8a7348075ec571536f44f9ae8c18bc1e02fed/libstdc%2B%2B-v3/include/std/optional#L1533

---

## Comment 1

> Username: alexweej  
> Created at: 2018-07-04 18:29:21 UTC  
> Url: https://github.com/boostorg/optional/issues/55#issuecomment-402539450  

OK it seems I'm a bit confused here. Seems that both `boost::optional<T>` and `boost::variant<Ts...>` work well inside `boost::unordered_set<T>`. But neither work inside an `std::unordered_set<T>`. Is there anything that can be done in Boost to make them work out of the box with the **`std`** hashing protocol?

---

## Comment 2

> Username: akrzemi1  
> Created at: 2018-07-05 07:11:50 UTC  
> Url: https://github.com/boostorg/optional/issues/55#issuecomment-402626926  

Sure. We can specialize `std::hash`, as was done in this implementation of optional: https://github.com/akrzemi1/Optional/blob/f6249e7fdcb80131c390a083f1621d96023e72e9/optional.hpp#L1038  
  
I have this recorded as a work item. But the deadline for features in 1.68 is now ended, so it will make it to 1.89, I think. In the meantime, you can specialize `std::hash` for `boost::optional` as in the enclosed link.

---

## Comment 3

> Username: alexweej  
> Created at: 2018-07-05 16:24:09 UTC  
> Url: https://github.com/boostorg/optional/issues/55#issuecomment-402778394  

@akrzemi1 I assume you meant 1.69! :) Would you also agree this is reasonable for boost::variant, too then? I can file an issue there too but would be hoping for your support.  
  
Thanks

---

## Comment 4

> Username: akrzemi1  
> Created at: 2018-07-06 10:21:14 UTC  
> Url: https://github.com/boostorg/optional/issues/55#issuecomment-402993834  

Yes, such addition makes sense for `boost::variant` as well.

---

## Comment 5

> Username: alexweej  
> Created at: 2018-07-06 10:55:08 UTC  
> Url: https://github.com/boostorg/optional/issues/55#issuecomment-403001029  

Thanks @akrzemi1, much appreciate your work here. If there's anything I can do to help let me know.

---

## Comment 6

> Username: KermMartian  
> Created at: 2022-05-19 17:23:13 UTC  
> Url: https://github.com/boostorg/optional/issues/55#issuecomment-1131984552  

Am I correct in thinking that `std::hash<boost::optional<T>>` is still pending?

---

## Comment 7

> Username: akrzemi1  
> Created at: 2022-05-20 20:16:35 UTC  
> Url: https://github.com/boostorg/optional/issues/55#issuecomment-1133318996  

Unfortunately, you are. Sorry for the delay. I'll try to fix it over the weekend.

---

## Comment 8

> Username: akrzemi1  
> Created at: 2022-05-24 15:12:06 UTC  
> Url: https://github.com/boostorg/optional/issues/55#issuecomment-1136057046  

This has now been implemented in branch develop. I would appreciate any feedback, if you can test it there.

---

## Comment 9

> Username: akrzemi1  
> Created at: 2022-07-30 08:41:27 UTC  
> Url: https://github.com/boostorg/optional/issues/55#issuecomment-1200118151  

And now it is in master, heading for 1.80.
