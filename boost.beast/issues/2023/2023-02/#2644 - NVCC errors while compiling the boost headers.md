# #2644 - NVCC errors while compiling the boost headers [Closed]

> Username: Jark5455  
> Created at: 2023-02-22 02:24:18 UTC  
> Updated at: 2024-12-01 09:31:13 UTC  
> Closed at: 2024-12-01 09:31:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2644  

Hello, I created a basic application to use boost beast and boost asio with CUDA. However, it appears that nvcc runs into a lot of errors while compiling the headers. The full error output is [here](https://paste.gg/p/anonymous/51c42314259c408e95992de93a8ec6f9).   
  
By any chance are there any precompiled headers for boost that I could use instead?

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-03-06 00:07:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2644#issuecomment-1455256876  

Are you including a `detail` header?

---

## Comment 2

> Username: Jark5455  
> Created at: 2023-03-06 04:06:23 UTC  
> Updated at: 2023-03-06 04:08:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2644#issuecomment-1455410479  

I don’t believe so, the only headers I have included are `boost/beast.hpp` `boost/asio.hpp` and `boost/bind.hpp`

---

## Comment 3

> Username: ashtum  
> Created at: 2024-12-01 09:31:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2644#issuecomment-2509655437  

@Jark5455, I couldn't find any issues when compiling Beast examples with nvc++. Feel free to reopen this issue if you have specific code that causes an error.
