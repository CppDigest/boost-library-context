# #91 - warning in to_time_t when std::time_t is 32-bit [Open]

> Username: RegardsIM  
> Created at: 2018-11-26 14:40:01 UTC  
> Updated at: 2018-11-26 15:08:44 UTC  
> Url: https://github.com/boostorg/date_time/issues/91  

I have 32-bit std::time_t and boost 1.67 installed  
Visual Studio gives me warning C4244: 'return': conversion from '__int64' to 'time_t', possible loss of data  
at boost\date_time\posix_time\conversion.hpp  
The warning is raised at boost::posix_time::to_time_t  
May be wrap return statement by static_cast<std::time_t> ? Is it safe enough?

---

## Comment 1

> Username: JeffGarland  
> Created at: 2018-11-26 14:49:54 UTC  
> Url: https://github.com/boostorg/date_time/issues/91#issuecomment-441664720  

Possibly since time_t is explicitly defined as an epoch of 1/1/1970 you shouldn't see an overflow.  But I think windows is moving to 64 bit time_t so I don't think we'd want to convert in that case.  So maybe some compile time foo to figure out if time_t is 32 bits and cast appropriately.

---

## Comment 2

> Username: RegardsIM  
> Created at: 2018-11-26 15:06:43 UTC  
> Updated at: 2018-11-26 15:08:44 UTC  
> Url: https://github.com/boostorg/date_time/issues/91#issuecomment-441671031  

May be. I just suggested static_cast based on commit from this issue [https://github.com/boostorg/date_time/pull/35#issue-107284825](url)  
May be due to last changes related to boost::int64_t in time_resolution_traits we have to find better solution
