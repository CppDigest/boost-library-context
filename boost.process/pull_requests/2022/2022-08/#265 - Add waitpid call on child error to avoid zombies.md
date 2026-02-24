# #265 Add waitpid call on child error to avoid zombies [Closed]

> Username: enrico-samknows  
> Created at: 2022-08-12 10:49:39 UTC  
> Updated at: 2022-08-18 16:50:04 UTC  
> Closed at: 2022-08-18 16:50:04 UTC  
> Url: https://github.com/boostorg/process/pull/265  

When using process, if execve fails due to non-existing program, I see zombies process appearing.  
With this patch I don't see zombies anymore.

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2022-08-18 15:27:40 UTC  
> Url: https://github.com/boostorg/process/pull/265#issuecomment-1219633406  

I'll look into the problem, but that doesn't look like a proper solution. It should capture the error.

---

## Comment 2

> Username: enrico-samknows  
> Created_at: 2022-08-18 16:46:58 UTC  
> Url: https://github.com/boostorg/process/pull/265#issuecomment-1219712648  

@klemens-morgenstern thanks for the reply. Feel free to fix it so it becomes compliant, or please send me more information about how you'd fix it, so I can try to improve it.

---
