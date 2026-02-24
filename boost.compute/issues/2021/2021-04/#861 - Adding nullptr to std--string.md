# #861 - Adding nullptr to std::string [Open]

> Username: psorokovnin-at-aligntech  
> Created at: 2021-04-20 15:17:51 UTC  
> Updated at: 2023-06-24 08:22:41 UTC  
> Url: https://github.com/boostorg/compute/issues/861  

In https://github.com/boostorg/compute/blob/develop/include/boost/compute/detail/path.hpp#L31 std::getenv can return nullptr. It happens for $HOME in my case and produces a segmentation fault. I don't know the specification for operator+(const char*, const std::string&), but I know that construction of std::string from nullptr is undefined behaviour.

---

## Comment 1

> Username: chetanpandey1266  
> Created at: 2022-08-30 17:26:28 UTC  
> Url: https://github.com/boostorg/compute/issues/861#issuecomment-1231956235  

Can you please assign this issue with me?
