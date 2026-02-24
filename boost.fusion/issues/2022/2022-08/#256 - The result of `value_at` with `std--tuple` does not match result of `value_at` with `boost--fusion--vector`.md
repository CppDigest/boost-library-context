# #256 - The result of `value_at` with `std::tuple` does not match result of `value_at` with `boost::fusion::vector` [Open]

> Username: denzor200  
> Created at: 2022-08-18 05:04:39 UTC  
> Updated at: 2022-11-27 11:43:15 UTC  
> Url: https://github.com/boostorg/fusion/issues/256  

https://godbolt.org/z/nWY9xvehE  
Looks like a issue in `std::tuple` integration.

---

## Comment 1

> Username: Kojoley  
> Created at: 2022-08-31 02:58:55 UTC  
> Url: https://github.com/boostorg/fusion/issues/256#issuecomment-1232395984  

Has to be because of this https://github.com/boostorg/fusion/blob/37ed70fc430cd651732bf761c7443d04d00815d4/include/boost/fusion/adapted/std_tuple/std_tuple_iterator.hpp#L36

---

## Comment 2

> Username: denzor200  
> Created at: 2022-11-27 11:43:15 UTC  
> Url: https://github.com/boostorg/fusion/issues/256#issuecomment-1328228350  

> Has to be because of this  
>   
> https://github.com/boostorg/fusion/blob/37ed70fc430cd651732bf761c7443d04d00815d4/include/boost/fusion/adapted/std_tuple/std_tuple_iterator.hpp#L36  
  
Should we fix it?
