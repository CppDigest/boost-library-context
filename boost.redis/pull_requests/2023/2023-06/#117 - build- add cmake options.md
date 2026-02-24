# #117 build: add cmake options [Merged]

> Username: hexorer  
> Created at: 2023-06-23 22:02:25 UTC  
> Updated at: 2023-07-01 11:03:32 UTC  
> Merged at: 2023-07-01 11:03:31 UTC  
> Closed at: 2023-07-01 11:03:31 UTC  
> Url: https://github.com/boostorg/redis/pull/117  

Added cmake options for install, tests, examples, and doc instead of always building them.  
  
options are enabled by default when building the project directly, otherwise if add_subdirectory (directly or by FetchContent, etc), then unnecessary options will be disabled.  
  
This PR is related to #115 issue.

---

## Comment 1

> Username: mzimbres  
> Created_at: 2023-06-24 07:38:49 UTC  
> Url: https://github.com/boostorg/redis/pull/117#issuecomment-1605299353  

Great, thanks.

---

## Comment 2

> Username: mzimbres  
> Created_at: 2023-06-24 07:44:59 UTC  
> Url: https://github.com/boostorg/redis/pull/117#issuecomment-1605300379  

Can you fix the windows build? It looks like the cpp20 code is failing to build.

---

## Comment 3

> Username: mzimbres  
> Created_at: 2023-06-24 07:54:00 UTC  
> Url: https://github.com/boostorg/redis/pull/117#issuecomment-1605301817  

I have just merged the branch that improves compilation times, You might want to rebase so that CI and local test will run faster.

---

## Comment 4

> Username: hexorer  
> Created_at: 2023-06-24 08:22:41 UTC  
> Url: https://github.com/boostorg/redis/pull/117#issuecomment-1605308096  

@mzimbres the workflow wasn't kicked to run after rebase, would you run it?

---

## Comment 5

> Username: mzimbres  
> Created_at: 2023-06-24 12:52:59 UTC  
> Url: https://github.com/boostorg/redis/pull/117#issuecomment-1605463004  

@cthulhu-irl: Windows builds are still broken.

---

## Comment 6

> Username: hexorer  
> Created_at: 2023-06-24 15:12:57 UTC  
> Updated_at: 2023-06-24 19:08:17 UTC  
> Url: https://github.com/boostorg/redis/pull/117#issuecomment-1605578362  

> @cthulhu-irl: Windows builds are still broken.  
  
@mzimbres it should be fixed now according to this: https://github.com/cthulhu-irl/redis/actions/runs/5365165804

---

## Comment 7

> Username: mzimbres  
> Created_at: 2023-06-25 06:12:35 UTC  
> Url: https://github.com/boostorg/redis/pull/117#issuecomment-1605890553  

Humm, not all examples should be run as tests.

---

## Comment 8

> Username: hexorer  
> Created_at: 2023-06-25 06:31:11 UTC  
> Updated_at: 2023-06-25 06:31:31 UTC  
> Url: https://github.com/boostorg/redis/pull/117#issuecomment-1605894058  

> Humm, not all examples should be run as tests.  
  
would you review the code to see what other things don't match the previous version's behavior?

---

## Comment 9

> Username: mzimbres  
> Created_at: 2023-06-25 07:13:41 UTC  
> Url: https://github.com/boostorg/redis/pull/117#issuecomment-1605906764  

Will do in the coming week. Examples like cpp20_streams, cpp20_subscriber run forever and should not be run as tests.

---

## Review 10 [Commented]

> Username: mzimbres  
> Created_at: 2023-06-25 12:28:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/117#pullrequestreview-1497042553  

📁 CMakeLists.txt

```diff
   5 |+ # determine whether it's main/root project
   6 |+ # or being built under another project.
   7 |+ if (NOT DEFINED BOOST_REDIS_MAIN_PROJECT)
```

> Username: mzimbres  
> Created_at: 2023-06-25 12:28:24 UTC  
> Url: https://github.com/boostorg/redis/pull/117#discussion_r1241155254  

This check will be always evaluated to false? Why can't we simply write  
  
```  
   if (CMAKE_CURRENT_SOURCE_DIR STREQUAL CMAKE_SOURCE_DIR)  
      set(BOOST_REDIS_MAIN_PROJECT ON)  
   else()  
      set(BOOST_REDIS_MAIN_PROJECT OFF)  
```

> Username: hexorer  
> Created_at: 2023-06-25 14:17:05 UTC  
> Url: https://github.com/boostorg/redis/pull/117#discussion_r1241200184  

In case for some reason user needed to define it manually, it would skip reassigning. specially when not using `add_subdirectory` but requiring the same behavior as if it was a subproject, for example using the library from another build system.  
  
I personally haven't came across such situations, and I've just copied and edited it for this project. but my guess is the above.


---

## Comment 11

> Username: mzimbres  
> Created_at: 2023-06-25 12:45:41 UTC  
> Url: https://github.com/boostorg/redis/pull/117#issuecomment-1606071168  

Also, cpp20 executables are being compiled as cpp17. For example, I get  
```  
$ ./build/g++-11/cpp20_intro  
Requires coroutine support.  
```  
Instead of the actual output.

---

## Comment 12

> Username: hexorer  
> Created_at: 2023-06-25 15:07:03 UTC  
> Updated_at: 2023-06-25 15:07:16 UTC  
> Url: https://github.com/boostorg/redis/pull/117#issuecomment-1606127315  

> Also, cpp20 executables are being compiled as cpp17. For example, I get  
>   
> ```  
> $ ./build/g++-11/cpp20_intro  
> Requires coroutine support.  
> ```  
>   
> Instead of the actual output.  
  
@mzimbres I think it should be fixed now. kinda weird that CI didn't yield the same error while running tests.  
  
```  
cthulhu@lolnet:~/projects/boost-redis$ ./build/g++-11/cpp20_intro  
(Boost.Redis) Resolve results: 127.0.0.1:6379  
(Boost.Redis) Connected to endpoint: 127.0.0.1:6379  
(Boost.Redis) Bytes written: 117  
(Boost.Redis) Hello: Success  
PING: Hello world  
(Boost.Redis) Connection lost: Operation canceled  
```

---

## Comment 13

> Username: mzimbres  
> Created_at: 2023-06-26 21:53:03 UTC  
> Url: https://github.com/boostorg/redis/pull/117#issuecomment-1608361867  

Looks good. I will have a final look tomorrow and then merge it. Thanks.

---
