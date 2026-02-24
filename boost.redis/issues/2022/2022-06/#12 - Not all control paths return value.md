# #12 - Not all control paths return value [Closed]

> Username: Eddie-cz  
> Created at: 2022-06-27 20:10:45 UTC  
> Updated at: 2022-07-03 06:17:49 UTC  
> Closed at: 2022-07-03 06:17:49 UTC  
> Url: https://github.com/boostorg/redis/issues/12  

aeedis\impl\error.ipp(47): error C4715: 'aedis::detail::error_category_impl::message': not all control paths return a value  
clang 14.0.6

---

## Comment 1

> Username: mzimbres  
> Created at: 2022-07-02 21:52:29 UTC  
> Url: https://github.com/boostorg/redis/issues/12#issuecomment-1172967998  

Thanks for reporting. I can't reproduce this on the master branch. Notice the high-level Aedis API was redesigned and the problem may have already been fixed.

---

## Comment 2

> Username: Eddie-cz  
> Created at: 2022-07-02 21:56:33 UTC  
> Url: https://github.com/boostorg/redis/issues/12#issuecomment-1172968684  

error.ipp, std::string message(int ev) fnc  
- clearly not returning value on case default, assert is not enough

---

## Comment 3

> Username: mzimbres  
> Created at: 2022-07-03 06:17:45 UTC  
> Url: https://github.com/boostorg/redis/issues/12#issuecomment-1173020141  

Fixed in the master. Thanks for reporting.
