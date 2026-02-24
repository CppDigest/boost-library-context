# #1 Remove Jamroot now that odeint is in boost tree. [Merged]

> Username: danieljames  
> Created at: 2014-05-04 13:03:13 UTC  
> Updated at: 2014-05-04 20:58:24 UTC  
> Merged at: 2014-05-04 20:58:24 UTC  
> Closed at: 2014-05-04 20:58:24 UTC  
> Url: https://github.com/boostorg/odeint/pull/1  

It's causing the documentation build to fail. Although you might prefer to create a jamfile based on it.

---

## Comment 1

> Username: headmyshoulder  
> Created_at: 2014-05-04 20:06:00 UTC  
> Url: https://github.com/boostorg/odeint/pull/1#issuecomment-42143167  

Ok, sure. We need to do that. But: Is there a way to force bjam to use the current jamfile as jamroot, such that we only rename Jamroot to Jamfile and use it as we use it now if we do a "standalone build" of odeint?

---

## Comment 2

> Username: danieljames  
> Created_at: 2014-05-04 20:25:42 UTC  
> Url: https://github.com/boostorg/odeint/pull/1#issuecomment-42143783  

I don't know, someone on the build list might have an idea.

---
