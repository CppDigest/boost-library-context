# #435 Suppress unused parameter warnings [Closed]

> Username: Flamefire  
> Created at: 2024-12-06 15:36:21 UTC  
> Updated at: 2024-12-20 07:56:40 UTC  
> Closed at: 2024-12-20 01:47:01 UTC  
> Url: https://github.com/boostorg/process/pull/435  



---

## Review 1 [Commented]

> Username: klemens-morgenstern  
> Created_at: 2024-12-13 03:33:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/process/pull/435#pullrequestreview-2501170819  

📁 include/boost/process/v2/posix/default_launcher.hpp

```diff
  56 |+                                   Init &&, base && )
  57 | {
  58 |+     boost::ignore_unused(executable, cmd_line);
```

> Username: klemens-morgenstern  
> Created_at: 2024-12-13 03:23:44 UTC  
> Updated_at: 2024-12-13 03:33:49 UTC  
> Url: https://github.com/boostorg/process/pull/435#discussion_r1883199852  

Why are you using ignore_unused for some and remove the names for others?

> Username: Flamefire  
> Created_at: 2024-12-13 08:25:04 UTC  
> Updated_at: 2024-12-13 08:25:05 UTC  
> Url: https://github.com/boostorg/process/pull/435#discussion_r1883542865  

I found this more understandable this way. The name of the argument in `Launcher & launcher` isn't required but for `const filesystem::path &executable` it does help in understanding what would be here. Similar for `error_code& ec`  
  
This allows easily verifying that the signature is correct.  
The alternatives are to use either approach consistently which is more code, or commenting out the parameter name instead of removing it.


---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2024-12-20 01:47:01 UTC  
> Url: https://github.com/boostorg/process/pull/435#issuecomment-2556098702  

Fixed in 662c0b10e3e46bca1c523c36b80511bd9c82d9ef

---

## Comment 3

> Username: Flamefire  
> Created_at: 2024-12-20 07:56:38 UTC  
> Url: https://github.com/boostorg/process/pull/435#issuecomment-2556470370  

Thanks!

---
