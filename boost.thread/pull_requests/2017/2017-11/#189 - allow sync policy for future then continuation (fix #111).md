# #189 allow sync policy for future then continuation (fix #111) [Merged]

> Username: gureedo  
> Created at: 2017-11-01 08:54:08 UTC  
> Updated at: 2017-12-21 17:40:27 UTC  
> Merged at: 2017-12-21 17:40:27 UTC  
> Closed at: 2017-12-21 17:40:27 UTC  
> Url: https://github.com/boostorg/thread/pull/189  

This PR is a proposal for #111 issue. This changes does not break backward compatibility.  
When appending continuation there are two cases: first if future is ready then continuation will be executed inplace, second future is not ready then continuation will be executed in thread that make this future ready.

---

## Comment 1

> Username: viboes  
> Created_at: 2017-11-07 17:45:18 UTC  
> Url: https://github.com/boostorg/thread/pull/189#issuecomment-342563926  

Hi, it is too late to merge this before delivering Boost.1.66.  
I'll take it in account after.

---
