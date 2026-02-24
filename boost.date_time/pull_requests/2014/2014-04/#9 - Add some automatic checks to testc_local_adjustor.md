# #9 Add some automatic checks to testc_local_adjustor. [Merged]

> Username: danieljames  
> Created at: 2014-04-07 22:15:00 UTC  
> Updated at: 2014-05-03 17:04:00 UTC  
> Merged at: 2014-05-03 17:04:00 UTC  
> Closed at: 2014-05-03 17:04:00 UTC  
> Url: https://github.com/boostorg/date_time/pull/9  

Test for 344c0dceb44ee0d0767ff041c2e627fdc9e45bab. It fails on 32-bit linux, which still uses 32-bit `std::time_t`. Since this is a posix wrapper, and this bug is due to a limitation of posix, I don't know if it's considered acceptable or not.

---
