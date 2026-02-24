# #40 implement moveability for date_time [Closed]

> Username: adrianimboden  
> Created at: 2019-02-14 16:40:15 UTC  
> Updated at: 2022-10-26 06:11:05 UTC  
> Closed at: 2022-10-26 06:11:05 UTC  
> Url: https://github.com/boostorg/locale/pull/40  

We use boost::locale with the icu backend for message processing in our software. Enabling move semantics for the date_time type results in a speed up of about 2x in our codebase.

---
