# #16 Add conversions to std:: counterparts when <system_error> is available [Merged]

> Username: pdimov  
> Created at: 2017-05-20 03:10:14 UTC  
> Updated at: 2017-05-21 15:27:10 UTC  
> Merged at: 2017-05-21 15:26:50 UTC  
> Closed at: 2017-05-21 15:26:50 UTC  
> Url: https://github.com/boostorg/system/pull/16  

This adds conversions from `boost::system::error_{category, error, condition}` to `std::` same. Very useful for people who are already on C++11 but need to use libraries such as Boost.ASIO that still report status via `boost::system::error_code`.

---

## Comment 1

> Username: DanielaE  
> Created_at: 2017-05-21 05:36:01 UTC  
> Url: https://github.com/boostorg/system/pull/16#issuecomment-302916461  

This seems to be very helpful. This solution is more complete than the tiny hand-rolled one specific to my particular needs regarding Boost.ASIO interop with standard C++ in my day-to-day use.

---
