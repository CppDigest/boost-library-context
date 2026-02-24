# #12 Remove the execution bit from source code files [Merged]

> Username: sukhodolin  
> Created at: 2016-02-05 06:47:00 UTC  
> Updated at: 2016-02-05 07:27:37 UTC  
> Merged at: 2016-02-05 07:27:24 UTC  
> Closed at: 2016-02-05 07:27:24 UTC  
> Url: https://github.com/boostorg/chrono/pull/12  

This bit doesn't cause any harm per see but produce unnecessary differences when the code is committed to (and synchronized with) other non-Git source control systems that have different rules for this bit.

---

## Comment 1

> Username: viboes  
> Created_at: 2016-02-05 07:27:37 UTC  
> Url: https://github.com/boostorg/chrono/pull/12#issuecomment-180233637  

I was unaware of this. Thanks.

---
