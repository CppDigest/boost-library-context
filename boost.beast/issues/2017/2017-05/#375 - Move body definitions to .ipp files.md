# #375 - Move body definitions to .ipp files [Closed]

> Username: vinniefalco  
> Created at: 2017-05-22 17:46:30 UTC  
> Updated at: 2017-06-08 19:46:38 UTC  
> Closed at: 2017-06-08 19:46:38 UTC  
> Url: https://github.com/boostorg/beast/issues/375  

Member function definitions of body algorithms should go in `impl/__.ipp` to hide them from casual readers and the doc toolchain.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-08 19:46:38 UTC  
> Url: https://github.com/boostorg/beast/issues/375#issuecomment-307207851  

On second thought, that would just create extra header material with little benefit.
