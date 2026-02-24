# #185 - Compilation issues in time_system_split for Apple clang version 12.0 [Closed]

> Username: wsobel  
> Created at: 2021-03-17 20:45:44 UTC  
> Updated at: 2022-08-17 05:47:40 UTC  
> Closed at: 2022-08-17 05:47:40 UTC  
> Url: https://github.com/boostorg/date_time/issues/185  

Seems like a clang bug causing the misinterpretation of a less-than operator.   
  
The following statement:  
  
    static BOOST_CXX14_CONSTEXPR  
    bool is_less(const time_rep_type& lhs, const time_rep_type& rhs)  
    {  
      if (lhs.day < rhs.day) return true;  
      if (lhs.day > rhs.day) return false;  
      return (lhs.time_of_day < rhs.time_of_day); // < being misinterpreted  
    }  
  
is generating an error: "Exected '>'"  
  
Workaround:  
  
    static BOOST_CXX14_CONSTEXPR  
    bool is_less(const time_rep_type& lhs, const time_rep_type& rhs)  
    {  
      if (lhs.day < rhs.day) return true;  
      if (lhs.day > rhs.day) return false;  
      return ((lhs.time_of_day) < rhs.time_of_day); // < being misinterpreted  
    }

---

## Comment 1

> Username: JeffGarland  
> Created at: 2021-03-18 00:33:29 UTC  
> Url: https://github.com/boostorg/date_time/issues/185#issuecomment-801529646  

Unfortunately we don't have apple platform as part of the boost test suite and I'm at a loss to understand how this can be misinterpreted and how the change fixes the issue. Is there an issue filed against apple clang somewhere? Not sure I want to make a change for a compiler this broken....

---

## Comment 2

> Username: mclow  
> Created at: 2021-03-18 00:47:37 UTC  
> Url: https://github.com/boostorg/date_time/issues/185#issuecomment-801534240  

I can try it - if I can get a complete test case.

---

## Comment 3

> Username: wsobel  
> Created at: 2021-03-18 01:31:20 UTC  
> Url: https://github.com/boostorg/date_time/issues/185#issuecomment-801549279  

Founds a fix by putting the includes for   
  
```  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/asio/steady_timer.hpp>  
```  
  
Before any other includes.  
  
There is some contention with another system library. I have not had time to check which file is conflicting.   
  
Thx - W  
  
> On Mar 17, 2021, at 17:47, Marshall Clow ***@***.***> wrote:  
>   
>   
> I can try it - if I can get a complete test case.  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub <https://github.com/boostorg/date_time/issues/185#issuecomment-801534240>, or unsubscribe <https://github.com/notifications/unsubscribe-auth/AAJ6BCKEG7IY37OPPRU4EWTTEFETTANCNFSM4ZLKLVMA>.  
>
