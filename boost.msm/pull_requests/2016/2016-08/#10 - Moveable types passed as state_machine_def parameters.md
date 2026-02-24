# #10 Moveable types passed as state_machine_def parameters [Closed]

> Username: bodzio528  
> Created at: 2016-08-10 17:46:31 UTC  
> Updated at: 2017-12-21 16:08:13 UTC  
> Closed at: 2017-12-21 16:08:13 UTC  
> Url: https://github.com/boostorg/msm/pull/10  

I have provided small extension in order to support moveable-only types passed as constructor parameters for state_machine_def (for example, std::unique_ptr). As a side efect, the maximum number of constructor parameter is no longer required to be defined a priori. Feature is enabled if variadic templates and rvalue reference C++11 features are available (tested with Boost.Config macro definitions), otherwise it falls back to legacy behavior.

---

## Comment 1

> Username: henry-ch  
> Created_at: 2017-12-21 16:08:04 UTC  
> Url: https://github.com/boostorg/msm/pull/10#issuecomment-353389605  

Except for the test, it seems to be a duplicate from 623c1677c905ea6c351266906bc726409e4b20ab.  
I added the test.

---
