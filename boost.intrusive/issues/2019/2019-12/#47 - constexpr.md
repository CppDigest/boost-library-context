# #47 - constexpr [Closed]

> Username: igoloe  
> Created at: 2019-12-19 19:41:46 UTC  
> Updated at: 2024-03-03 19:40:32 UTC  
> Closed at: 2020-01-16 14:36:41 UTC  
> Url: https://github.com/boostorg/intrusive/issues/47  

I am using intrusive now for a very long time. From a memory managment perspective it maybe possible to make intrusie constexpr? Ofc reinterpret_cast or void* may be a problem.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2020-01-16 14:36:41 UTC  
> Url: https://github.com/boostorg/intrusive/issues/47#issuecomment-575179801  

It seems to me that constexpr-ing would be quite difficult. Even with base hooks, there are too many casts to support it.

---

## Comment 2

> Username: burnpanck  
> Created at: 2024-03-03 19:40:31 UTC  
> Url: https://github.com/boostorg/intrusive/issues/47#issuecomment-1975275445  

How about a constexpr default constructor? This would enable using intrusive containers in `constinit` globals, which we use extensively in our embedded bare-metal firmware. Knowing no "user code" runs early helps peace of mind that there will be no failures before the OS itself boots.
