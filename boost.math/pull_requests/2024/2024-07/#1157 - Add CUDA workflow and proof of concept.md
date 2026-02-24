# #1157 Add CUDA workflow and proof of concept [Closed]

> Username: mborland  
> Created at: 2024-07-16 14:01:02 UTC  
> Updated at: 2024-07-19 17:15:28 UTC  
> Closed at: 2024-07-19 17:15:28 UTC  
> Url: https://github.com/boostorg/math/pull/1157  



---

## Comment 1

> Username: mborland  
> Created_at: 2024-07-16 14:43:21 UTC  
> Url: https://github.com/boostorg/math/pull/1157#issuecomment-2231100995  

@sdarwin I added a workflow with `runs-on: gpu-runner-1`, but the job doesn't seem to be getting picked up. Is there something else that I need to add?

---

## Comment 2

> Username: sdarwin  
> Created_at: 2024-07-16 15:30:35 UTC  
> Url: https://github.com/boostorg/math/pull/1157#issuecomment-2231238188  

There may be more than one blocker.    
  
The first step:  enabling a larger GitHub actions runner requires being an Owner of an organization, and then configuring billing and spending limits.   I'm not able to do this on `boostorg` so gpu-runner-1 exists in `cppalliance`.     
  
We might explore the options of setting up billing in `boostorg`.   However, since the effectiveness of the gpu tests is still unknown, how about creating a repository in `cppalliance` ?

---

## Comment 3

> Username: mborland  
> Created_at: 2024-07-16 15:39:50 UTC  
> Url: https://github.com/boostorg/math/pull/1157#issuecomment-2231259943  

> There may be more than one blocker.  
>   
> The first step: enabling a larger GitHub actions runner requires being an Owner of an organization, and then configuring billing and spending limits. I'm not able to do this on `boostorg` so gpu-runner-1 exists in `cppalliance`.  
>   
> We might explore the options of setting up billing in `boostorg`. However, since the effectiveness of the gpu tests is still unknown, how about creating a repository in `cppalliance` ?  
  
I'll try a similar PR on `cppalliance/decimal` since the concept is the same.

---
