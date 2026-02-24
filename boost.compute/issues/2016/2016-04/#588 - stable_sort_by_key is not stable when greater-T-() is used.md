# #588 - stable_sort_by_key is not stable when greater<T>() is used [Closed]

> Username: pavanky  
> Created at: 2016-04-21 22:20:59 UTC  
> Updated at: 2016-04-26 07:19:34 UTC  
> Closed at: 2016-04-26 07:19:29 UTC  
> Url: https://github.com/boostorg/compute/issues/588  

- `sort_by_key` uses `radix_sort_by_key` which only supports `less<T>()`.  
- `sort_by_key` for `greater<T>()` [was implemented](https://github.com/boostorg/compute/pull/345) by simply reversing both the outputs from `less<T>()`. Which makes it **not** stable.  
- `stable_sort_by_key` just wraps `sort_by_key` thereby not being stable when `greater<T>` is called.

---

## Comment 1

> Username: pavanky  
> Created at: 2016-04-21 22:31:16 UTC  
> Url: https://github.com/boostorg/compute/issues/588#issuecomment-213142646  

One possible hack would be to do the following:  
  
1) Negate the input keys  
2) Sort in ascending order  
3) Negate the output keys.

---

## Comment 2

> Username: jszuppe  
> Created at: 2016-04-22 06:38:08 UTC  
> Updated at: 2016-04-22 06:48:29 UTC  
> Url: https://github.com/boostorg/compute/issues/588#issuecomment-213288315  

Good points. Hmm.. Negating the input keys in the algorithms of radix_sort is a good idea (quick and cheap in terms of performance), but it won't work for unsigned keys. We need to locate the place in the radix_sort algorithm where we can switch something and get descending order.  
  
On the other hand, we can just change unsigned values so 0 becomes `MAX_UINT` etc. Would that work?

---

## Comment 3

> Username: pavanky  
> Created at: 2016-04-22 07:36:45 UTC  
> Url: https://github.com/boostorg/compute/issues/588#issuecomment-213303143  

> Negating the input keys in the algorithms of radix_sort is a good idea (quick and cheap in terms of performance), but it won't work for unsigned keys. We need to locate the place in the radix_sort algorithm where we can switch something and get descending order.  
  
Wouldn't the same place where you are doing float --> int transform for sort on floats be ideal ? I think you just need to have another ifdef for [radix](https://github.com/boostorg/compute/blob/master/include/boost/compute/algorithm/detail/radix_sort.hpp#L95) function for descending order.  
  
> On the other hand, we can just change unsigned values so 0 becomes MAX_UINT etc. Would that work?  
  
This is [exactly what we are doing](https://github.com/shehzan10/arrayfire/blob/sort/src/backend/opencl/kernel/sort_by_key_impl.hpp#L85) on our end as a work around for now.

---

## Comment 4

> Username: jszuppe  
> Created at: 2016-04-22 08:21:55 UTC  
> Url: https://github.com/boostorg/compute/issues/588#issuecomment-213321554  

Ok. Thanks. Currently, I'm abroad but I'll look into it on Sunday or Monday.  
22 kwi 2016 09:36 "Pavan Yalamanchili" notifications@github.com  
napisał(a):  
  
> Negating the input keys in the algorithms of radix_sort is a good idea  
> (quick and cheap in terms of performance), but it won't work for unsigned  
> keys. We need to locate the place in the radix_sort algorithm where we can  
> switch something and get descending order.  
>   
> Wouldn't the same place where you are doing float --> int transform for  
> sort on floats be ideal ? I think you just need to have another ifdef for  
> radix  
> https://github.com/boostorg/compute/blob/master/include/boost/compute/algorithm/detail/radix_sort.hpp#L95  
> function for descending order.  
>   
> On the other hand, we can just change unsigned values so 0 becomes  
> MAX_UINT etc. Would that work?  
>   
> This is exactly what we are doing  
> https://github.com/shehzan10/arrayfire/blob/sort/src/backend/opencl/kernel/sort_by_key_impl.hpp#L85  
> on our end as a work around for now.  
>   
> —  
> You are receiving this because you commented.  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/compute/issues/588#issuecomment-213303143

---

## Comment 5

> Username: jszuppe  
> Created at: 2016-04-26 07:15:37 UTC  
> Url: https://github.com/boostorg/compute/issues/588#issuecomment-214647224  

This can be closed. Fixed by https://github.com/boostorg/compute/pull/593.
