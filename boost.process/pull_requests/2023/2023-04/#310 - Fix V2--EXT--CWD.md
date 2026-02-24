# #310 Fix V2::EXT::CWD [SunOS] [Merged]

> Username: ghost  
> Created at: 2023-04-26 19:36:01 UTC  
> Updated at: 2024-08-05 23:51:25 UTC  
> Merged at: 2023-10-12 13:43:59 UTC  
> Closed at: 2023-10-12 13:43:59 UTC  
> Url: https://github.com/boostorg/process/pull/310  

filesystem::canonical is basically the same thing as realpath on Unix-likes, which only resolves one symbolic link. If one symbolic link points to yet another symbolic link and so on and so forth, it will not resolve all symbolic links. It will only do one link for each call to canonical. On SunOS, unlike Linux, /proc/${pid}/cwd does not directly point to the literal current working directory of the given ${pid}. Instead, it will point to yet another symlink - /proc/${pid}/path/cwd which once you have followed that second link only then will you have the literal cwd path for the process id.

---

## Comment 1

> Username: ghost  
> Created_at: 2023-05-25 07:34:20 UTC  
> Url: https://github.com/boostorg/process/pull/310#issuecomment-1562421533  

lol this killed the ci for basically no reason

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2023-05-25 07:47:32 UTC  
> Url: https://github.com/boostorg/process/pull/310#issuecomment-1562437533  

Looks good.

---
