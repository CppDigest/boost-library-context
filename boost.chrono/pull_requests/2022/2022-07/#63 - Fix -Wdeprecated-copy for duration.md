# #63 Fix -Wdeprecated-copy for duration [Merged]

> Username: mborland  
> Created at: 2022-07-03 03:06:21 UTC  
> Updated at: 2022-07-03 18:33:20 UTC  
> Merged at: 2022-07-03 18:19:33 UTC  
> Closed at: 2022-07-03 18:19:33 UTC  
> Url: https://github.com/boostorg/chrono/pull/63  

Adds definition of copy constructor for `boost::chrono::duration` to fix `warning: definition of implicit copy constructor for 'duration' is deprecated because it has a user-declared copy assignment operator`.

---

## Comment 1

> Username: mborland  
> Created_at: 2022-07-03 14:49:33 UTC  
> Url: https://github.com/boostorg/chrono/pull/63#issuecomment-1173105090  

@jzmaddock or @pdimov can you please kick off the CI run for this since I am a first time chrono contributor?

---
