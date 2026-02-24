# #149 - Attach source line information to error codes [Open]

> Username: anarthal  
> Created at: 2023-05-19 10:58:11 UTC  
> Updated at: 2023-05-22 08:35:05 UTC  
> Url: https://github.com/boostorg/mysql/issues/149  

Currently, error codes have no associated line info, and thrown exceptions hold the exception-checking line, rather than the line where the error originated from.

---

## Comment 1

> Username: anarthal  
> Created at: 2023-05-22 08:35:05 UTC  
> Url: https://github.com/boostorg/mysql/issues/149#issuecomment-1556782671  

Specially important in code like `readable_field_traits`, where the same error code gets returned in more than a single situation.
