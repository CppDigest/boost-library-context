# #167 Added return *this in move assignment operator [Merged]

> Username: zaucy  
> Created at: 2020-08-02 01:55:24 UTC  
> Updated at: 2020-08-03 17:24:41 UTC  
> Merged at: 2020-08-03 09:29:10 UTC  
> Closed at: 2020-08-03 09:29:10 UTC  
> Url: https://github.com/boostorg/process/pull/167  

I caught this while compiling with clang 10.0.0 with `-Werror,-Wreturn-type`. I'm unsure if the lack of a return statement was intentional

---
