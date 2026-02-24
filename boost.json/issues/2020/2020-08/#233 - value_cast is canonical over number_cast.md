# #233 - value_cast is canonical over number_cast [Closed]

> Username: vinniefalco  
> Created at: 2020-08-29 22:45:41 UTC  
> Updated at: 2020-09-20 20:46:30 UTC  
> Closed at: 2020-09-20 20:46:30 UTC  
> Url: https://github.com/boostorg/json/issues/233  

Instead of teaching `number_cast` for scalars we can just use `value_cast`. Thus remove `number_cast` from the public APIs, and just call the implementation from `value_to`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-09-20 20:46:30 UTC  
> Url: https://github.com/boostorg/json/issues/233#issuecomment-695834334  

Unfortunately we need `number_cast` for the error code
