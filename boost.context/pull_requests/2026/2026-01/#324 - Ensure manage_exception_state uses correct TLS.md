# #324 Ensure manage_exception_state uses correct TLS [Open]

> Username: zhykzhykzhyk  
> Created at: 2026-01-04 17:13:24 UTC  
> Updated at: 2026-02-23 06:51:31 UTC  
> Url: https://github.com/boostorg/context/pull/324  

Solves #323

---

## Comment 1

> Username: CreoValis  
> Created_at: 2026-02-09 18:26:20 UTC  
> Url: https://github.com/boostorg/context/pull/324#issuecomment-3873252607  

Wouldn't access through a `volatile *` be a better solution here? The "no inline" spec probably works around the issue, but that just seems to push the dtor body to the point where gcc couldn't "see into it" to optimize it away.  
I think using a volatile pointer cast would forbid it from even optimizing it away.  
  
Like so:  
````cpp  
    manage_exception_state() {  
        exception_state_ = *(volatile __cxa_eh_globals *)__cxa_get_globals();  
    }  
    ~manage_exception_state() {  
        *(volatile __cxa_eh_globals *)__cxa_get_globals() = exception_state_;  
    }  
`

---
