# #1066 - [Question] Build Only Nessisary Boost Libraries [Open]

> Username: TheFloatingBrain  
> Created at: 2025-07-17 20:40:56 UTC  
> Updated at: 2025-08-31 04:38:37 UTC  
> Url: https://github.com/boostorg/boost/issues/1066  

I just deleted my build directory and discovered nearly 5GB of data in it, I think that I am building ALL boost libraries, but I only really need compute and its dependencies, is there a way to express this in cmake. I am using CMake FetchContent?

---

## Comment 1

> Username: nigels-com  
> Created at: 2025-08-31 04:38:36 UTC  
> Url: https://github.com/boostorg/boost/issues/1066#issuecomment-3239752073  

Would a shallow clone of the submodules suffice?
