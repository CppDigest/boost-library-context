# #13 fix a mistake in process_times<Rep>::read() [Merged]

> Username: rick68  
> Created at: 2016-06-19 09:30:08 UTC  
> Updated at: 2016-06-28 12:00:38 UTC  
> Merged at: 2016-06-28 12:00:30 UTC  
> Closed at: 2016-06-28 12:00:30 UTC  
> Url: https://github.com/boostorg/chrono/pull/13  

In L202, the input stream will be used to retrieve values, but it does not ignore first character '{'

---

## Comment 1

> Username: viboes  
> Created_at: 2016-06-28 12:00:38 UTC  
> Url: https://github.com/boostorg/chrono/pull/13#issuecomment-229028731  

Thanks.

---
