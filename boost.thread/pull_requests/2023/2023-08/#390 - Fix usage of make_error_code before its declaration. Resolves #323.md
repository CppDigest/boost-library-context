# #390 Fix usage of make_error_code before its declaration. Resolves #323. [Open]

> Username: kuzkry  
> Created at: 2023-08-14 19:15:28 UTC  
> Updated at: 2023-08-14 19:15:28 UTC  
> Url: https://github.com/boostorg/thread/pull/390  

Moves the concrete definition of `future_errc`  
to the auxiliary "detail" namespace, making ADL look in there.  
Employs 'using' statements for the reuse of extant  
`make_error_code` and `make_error_condition` functions, along with  
ensuring backward compatibility.

---
