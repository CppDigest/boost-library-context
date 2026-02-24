# #960 - pausation should post [Closed]

> Username: vinniefalco  
> Created at: 2018-01-01 22:26:06 UTC  
> Updated at: 2018-01-02 17:24:20 UTC  
> Closed at: 2018-01-02 17:24:20 UTC  
> Url: https://github.com/boostorg/beast/issues/960  

We can reduce the amount of code and guarantee the proper invariant by making `pausation` call `asio::post` when it resumes the operation. This will require passing the `executor` in the initial call to `emplace`, and correctly type-erasing it.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-01-02 17:24:20 UTC  
> Url: https://github.com/boostorg/beast/issues/960#issuecomment-354821364  

Actually this won't work because usually a composed operation acquires a read or write block before posting.
