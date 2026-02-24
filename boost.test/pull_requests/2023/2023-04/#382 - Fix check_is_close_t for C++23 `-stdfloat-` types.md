# #382 Fix check_is_close_t for C++23 `<stdfloat>` types [Merged]

> Username: mborland  
> Created at: 2023-04-25 10:51:18 UTC  
> Updated at: 2023-05-30 15:27:52 UTC  
> Merged at: 2023-05-30 15:27:49 UTC  
> Closed at: 2023-05-30 15:27:49 UTC  
> Url: https://github.com/boostorg/test/pull/382  

Implicit conversions are ill-formed so make them explicit

---

## Comment 1

> Username: mborland  
> Created_at: 2023-05-30 15:27:33 UTC  
> Url: https://github.com/boostorg/test/pull/382#issuecomment-1568641438  

Ubuntu 18.04 has been removed from Github actions so need to use containers. Since this is a typedef and a cast I think it is safe for now.

---
