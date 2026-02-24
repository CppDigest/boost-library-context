# #149 - interprocess_condition_any :: timed_wait update [Closed]

> Username: AndrejBazhan  
> Created at: 2021-07-23 17:35:43 UTC  
> Updated at: 2021-08-18 22:38:39 UTC  
> Closed at: 2021-08-18 22:38:39 UTC  
> Url: https://github.com/boostorg/interprocess/issues/149  

Update interprocess_condition and interprocess_condition_any to use chrono time system insted of posix please.  
And in would be better to provide wait_for and wait_until methods like std::condition_variable.  
  
Also !lock doesn't make sence for L template of interprocess_condition_any, it's not a pointer;  
  
bool timed_wait(L& lock, const boost::posix_time::ptime &abs_time)  
   {  
      if (!lock)  
         throw lock_exception();  
      return algo_type::wait(m_data, lock, true, abs_time);  
   }

---

## Comment 1

> Username: AndrejBazhan  
> Created at: 2021-07-26 15:20:20 UTC  
> Updated at: 2021-08-02 06:18:50 UTC  
> Url: https://github.com/boostorg/interprocess/issues/149#issuecomment-886796847  

Right now I reproduce std interface like this:  
  
```  
std::cv_status wait_for(L& lock, const std::chrono::milliseconds& rel_time) {  
       boost::posix_time::ptime p(boost::gregorian::date(1970, 1, 1),   
           boost::posix_time::milliseconds(  
               (std::chrono::duration_cast<std::chrono::milliseconds>(std::chrono::system_clock().now().time_since_epoch())   
                   + rel_time).count()  
           ));  
       return m_cond.timed_wait(lock, p) ? std::cv_status::no_timeout : std::cv_status::timeout;  
   }  
```

---

## Comment 2

> Username: igaztanaga  
> Created at: 2021-08-12 23:35:28 UTC  
> Url: https://github.com/boostorg/interprocess/issues/149#issuecomment-898042111  

Since Boost 1.76 Interprocess already supports std/boost::chrono. Keeping the issue open to review the "!lock" part & wait_for.

---

## Comment 3

> Username: AndrejBazhan  
> Created at: 2021-08-13 06:22:28 UTC  
> Updated at: 2021-08-13 07:17:10 UTC  
> Url: https://github.com/boostorg/interprocess/issues/149#issuecomment-898221244  

> Since Boost 1.76 Interprocess already supports std/boost::chrono. Keeping the issue open to review the "!lock" part & wait_for.  
  
interprocess_condition_any and other classes in 1.76 Interprocess I've seen are using posix time, not chrono. And default installation doesn't hame posix time lib, it's inconvinient. Best thing is to move to chrono completely. And to add methods like std classes - wait_for, wait_until. So they can be used interchangably in templates.

---

## Comment 4

> Username: igaztanaga  
> Created at: 2021-08-13 21:56:48 UTC  
> Url: https://github.com/boostorg/interprocess/issues/149#issuecomment-898739630  

Sorry, I meant Boost 1.77:  
  
https://www.boost.org/doc/libs/1_77_0/boost/interprocess/sync/interprocess_condition_any.hpp  
  
As interprocess classes were created before the standard classes, member function names are different, but I agree that adding the standard member functions will help users.

---

## Comment 5

> Username: igaztanaga  
> Created at: 2021-08-18 22:33:58 UTC  
> Url: https://github.com/boostorg/interprocess/issues/149#issuecomment-901472866  

Taking this issue a bit further, I'll implement a standard interface for all synchronization objects (mutexes, condition variables, file locks) so that standard utilities can be used. There is a problem though, with condition variables, as Interprocess (as Boost.Thread) should return its own cv_status. In the future, we can study if this cv_status could be convertible to std::cv_status or that possibility is a bad idea.
