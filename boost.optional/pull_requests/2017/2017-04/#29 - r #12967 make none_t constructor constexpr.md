# #29 r #12967 make none_t constructor constexpr [Merged]

> Username: citrusmoose  
> Created at: 2017-04-13 13:35:19 UTC  
> Updated at: 2020-10-24 15:20:51 UTC  
> Merged at: 2020-10-24 15:19:44 UTC  
> Closed at: 2020-10-24 15:19:44 UTC  
> Url: https://github.com/boostorg/optional/pull/29  

https://svn.boost.org/trac/boost/ticket/12967

---

## Comment 1

> Username: MarcelRaad  
> Created_at: 2017-08-16 14:08:37 UTC  
> Url: https://github.com/boostorg/optional/pull/29#issuecomment-322783067  

This will break compilers without constexpr support. You could use `BOOST_CONSTEXPR` from Boost.Config.

---

## Comment 2

> Username: citrusmoose  
> Created_at: 2017-08-16 17:59:18 UTC  
> Url: https://github.com/boostorg/optional/pull/29#issuecomment-322851086  

Updated; thanks for pointing that out @MarcelRaad

---

## Comment 3

> Username: vlichevsky  
> Created_at: 2020-10-24 09:31:03 UTC  
> Url: https://github.com/boostorg/optional/pull/29#issuecomment-715887964  

Hi! A friendly ping from boost::optional user. Are there any obstacles to merging this pull request? It would be very nice if it got some attention.

---

## Comment 4

> Username: akrzemi1  
> Created_at: 2020-10-24 15:20:51 UTC  
> Url: https://github.com/boostorg/optional/pull/29#issuecomment-715931524  

Thank you for the reminder. It is now merged to develop.

---
