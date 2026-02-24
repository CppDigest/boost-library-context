# #192 ptime from iso error string error with date only [Merged]

> Username: JeffGarland  
> Created at: 2021-05-02 14:39:56 UTC  
> Updated at: 2022-02-12 02:13:58 UTC  
> Merged at: 2021-05-02 15:57:37 UTC  
> Closed at: 2021-05-02 15:57:37 UTC  
> Url: https://github.com/boostorg/date_time/pull/192  

fix one case -- check the error handling doesn't dip into std::string out of range.

---
