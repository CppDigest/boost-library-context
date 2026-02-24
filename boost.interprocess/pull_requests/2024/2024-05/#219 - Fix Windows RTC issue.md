# #219 Fix Windows RTC issue [Merged]

> Username: k3DW  
> Created at: 2024-05-16 03:27:51 UTC  
> Updated at: 2024-05-19 16:43:19 UTC  
> Merged at: 2024-05-16 19:25:37 UTC  
> Closed at: 2024-05-16 19:25:37 UTC  
> Url: https://github.com/boostorg/interprocess/pull/219  

I reproduced [this](https://github.com/cmazakas/unordered/actions/runs/9035399323/job/24830087065#step:6:1478) failing CI run of Boost.Unordered locally, and found that it gave me a pop-up coming from Boost.Interprocess.  
![image](https://github.com/boostorg/interprocess/assets/10316270/b057978d-a484-45d5-b6c7-cccf35eb042c)  
After the change in this PR, the issue no longer occurs.

---
