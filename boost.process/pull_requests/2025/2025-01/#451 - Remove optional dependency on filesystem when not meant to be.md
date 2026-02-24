# #451 Remove optional dependency on filesystem when not meant to be [Merged]

> Username: SimonMaracine  
> Created at: 2025-01-14 14:05:17 UTC  
> Updated at: 2025-02-21 00:09:42 UTC  
> Merged at: 2025-02-21 00:09:42 UTC  
> Closed at: 2025-02-21 00:09:42 UTC  
> Url: https://github.com/boostorg/process/pull/451  

Build previously failed when BOOST_PROCESS_USE_STD_FS was ON, because filesystem was linked to regardless.  
  
I tried to use this library with the std version of filesystem and I noticed that Boost::filesystem was listed as a dependency regardless of the flag. I'm pretty sure this is just a little mistake.

---

## Comment 1

> Username: SimonMaracine  
> Created_at: 2025-01-23 21:11:35 UTC  
> Updated_at: 2025-01-23 21:11:53 UTC  
> Url: https://github.com/boostorg/process/pull/451#issuecomment-2611026344  

I am very sorry. I just noticed that I have made the commit in the `develop` branch instead of a separate one. I haven't made a contribution in a long time.  
  
I think it's better to just close this pull request and someone else make the change manually.

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2025-01-26 13:50:11 UTC  
> Url: https://github.com/boostorg/process/pull/451#issuecomment-2614432759  

You pushed to your develop branch, that's fine.

---
