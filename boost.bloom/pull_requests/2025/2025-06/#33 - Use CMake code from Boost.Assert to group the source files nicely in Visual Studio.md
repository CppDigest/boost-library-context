# #33 Use CMake code from Boost.Assert to group the source files nicely in Visual Studio [Merged]

> Username: k3DW  
> Created at: 2025-06-25 10:30:31 UTC  
> Updated at: 2025-07-01 17:22:34 UTC  
> Merged at: 2025-07-01 17:03:58 UTC  
> Closed at: 2025-07-01 17:03:58 UTC  
> Url: https://github.com/boostorg/bloom/pull/33  

Addresses #15

---

## Review 1 [Commented]

> Username: joaquintides  
> Created_at: 2025-06-25 14:15:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/bloom/pull/33#pullrequestreview-2958363660  

📁 cmake/GroupSources.cmake

```diff
   6 |+ #
   7 |+ 
   8 |+ # This code is take from the Boost.Beast library
```

> Username: joaquintides  
> Created_at: 2025-06-25 14:15:44 UTC  
> Updated_at: 2025-06-25 14:15:45 UTC  
> Url: https://github.com/boostorg/bloom/pull/33#discussion_r2166837994  

s/take/taken


---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2025-06-25 14:40:44 UTC  
> Url: https://github.com/boostorg/bloom/pull/33#issuecomment-3005048783  

An automated preview of the documentation is available at [https://33.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html](https://33.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html)

---

## Review 3 [Commented]

> Username: joaquintides  
> Created_at: 2025-06-25 16:49:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/bloom/pull/33#pullrequestreview-2958940142  

📁 CMakeLists.txt

```diff
  32 |+ 
  33 |+ file(GLOB_RECURSE boost_bloom_all_headers "include/**/*.hpp")
  34 |+ target_sources(boost_bloom PUBLIC FILE_SET headers TYPE HEADERS FILES "${boost_bloom_all_headers}")
```

> Username: joaquintides  
> Created_at: 2025-06-25 16:49:24 UTC  
> Url: https://github.com/boostorg/bloom/pull/33#discussion_r2167179847  

This failed me locally. ChatGPT says FILE_SET is CMake 3.23 or later and suggests:  
```  
if(CMAKE_VERSION VERSION_GREATER_EQUAL "3.23")  
  file(GLOB_RECURSE boost_bloom_all_headers "include/**/*.hpp")  
  target_sources(boost_bloom PUBLIC FILE_SET headers TYPE HEADERS FILES "${boost_bloom_all_headers}")  
endif()  
```  
To be totally clear, I don't know if this approach is correct.

> Username: k3DW  
> Created_at: 2025-06-29 10:05:42 UTC  
> Url: https://github.com/boostorg/bloom/pull/33#discussion_r2173697083  

I fixed it now, it should work for earlier versions. I removed the usage of [File Sets](https://cmake.org/cmake/help/latest/command/target_sources.html#file-sets)


---

## Comment 4

> Username: k3DW  
> Created_at: 2025-06-29 15:29:34 UTC  
> Url: https://github.com/boostorg/bloom/pull/33#issuecomment-3016789228  

> Your latest commit breaks CMake-based GHA jobs:  
  
Got it. I'm trying the `RELATIVE_PATH` argument

---

## Comment 5

> Username: k3DW  
> Created_at: 2025-06-29 16:20:10 UTC  
> Url: https://github.com/boostorg/bloom/pull/33#issuecomment-3016827819  

> Got it. I'm trying the `RELATIVE_PATH` argument  
  
Didn't work. I decided to just put the header files as sources in the `add_library()` call.

---

## Comment 6

> Username: joaquintides  
> Created_at: 2025-06-29 16:35:25 UTC  
> Url: https://github.com/boostorg/bloom/pull/33#issuecomment-3016839786  

GHA now works, thank you! I've cloned the repo and opened it up with Visual Studio, which recognized the `CMakeLists.txt` without problems. When I click on the CMake Targets view of the Solution Explorer, I see this:  
  
![imagen](https://github.com/user-attachments/assets/78bd939f-4788-435a-9b9c-bffa401ec24f)  
  
Note the `boost` folder pointed to by the red arrow. Shouldn't it include the `bloom` subfolder and its files? Instead, we have that `bloom` subfolder as a top-level entry.

---

## Comment 7

> Username: k3DW  
> Created_at: 2025-06-29 17:51:31 UTC  
> Url: https://github.com/boostorg/bloom/pull/33#issuecomment-3016917296  

> When I click on the CMake Targets view of the Solution Explorer, I see this:  
  
That's interesting, I don't get the same thing on my end. But I pushed an extra call to `source_group()` to override this behaviour just in case

---

## Comment 8

> Username: joaquintides  
> Created_at: 2025-06-30 07:08:15 UTC  
> Url: https://github.com/boostorg/bloom/pull/33#issuecomment-3018043490  

Nothing's changed on my side, `bloom` is still a top-level entry instead of being under `boost`. Microsoft Visual Studio Community 2022 (64-bit) - Current Version 17.2.3.

---

## Comment 9

> Username: pdimov  
> Created_at: 2025-06-30 08:31:39 UTC  
> Url: https://github.com/boostorg/bloom/pull/33#issuecomment-3018269393  

Why not just use something that works, like https://github.com/boostorg/assert/blob/5dcb2af5213ae132b7531e45e7f93258cc33ffd8/CMakeLists.txt#L20-L27

---

## Comment 10

> Username: joaquintides  
> Created_at: 2025-06-30 09:10:12 UTC  
> Url: https://github.com/boostorg/bloom/pull/33#issuecomment-3018386603  

I can confirm this works locally, and `cmake/GroupSources.cmake` is not needed with Peter's approach:  
  
![imagen](https://github.com/user-attachments/assets/8a6f3531-d858-4746-947a-c6fd09a5bbbd)

---

## Comment 11

> Username: k3DW  
> Created_at: 2025-07-01 16:02:32 UTC  
> Url: https://github.com/boostorg/bloom/pull/33#issuecomment-3024638284  

Agreed, Peter's suggestion is much nicer. I've pushed that now

---

## Comment 12

> Username: joaquintides  
> Created_at: 2025-07-01 17:03:50 UTC  
> Url: https://github.com/boostorg/bloom/pull/33#issuecomment-3024858506  

Thank you Braden and Peter, merging now (just in time before the deadline for Boost 1.89). Let me add some comments, though. If I clone the repo and open the folder directly with Visual Studio, this is what I get:  
  
![imagen](https://github.com/user-attachments/assets/9bda98a0-1aff-43f6-983b-18b7758998cf)  
  
So, no files shown at all. This is due to this check:  
```  
if(CMAKE_VERSION VERSION_GREATER 3.18 AND CMAKE_GENERATOR MATCHES "Visual Studio")  
```  
because the generator chosen by VS in this situation is Ninja. If I do  
```  
cmake -G "Visual Studio 17 2022" -A x64 -S . -B build-vs  
```  
then a `.sln` file is generated, and opening that does show all the files:  
  
![imagen](https://github.com/user-attachments/assets/c8da708c-6941-4dbc-9f8e-75293ba7df23)  
  
I wonder why the check mentioned above does not allow for `CMAKE_GENERATOR` being "Ninja" in addition to "Visual Studio" (seems to work from my local experiments). To be clear, I'm not asking for further changes in this PR, since all other `CMakeLists.txt`s  I've seen in Boost do the same as this one: I'm just curious.

---

## Comment 13

> Username: pdimov  
> Created_at: 2025-07-01 17:22:34 UTC  
> Url: https://github.com/boostorg/bloom/pull/33#issuecomment-3024913453  

You can replace `MATCHES "Visual Studio"` with `MATCHES "Visual Studio|Ninja"` if you like. The reason the check is so conservative is that adding sources to a header-only library can do who knows what depending on the generator and the platform so you'll need a lot of test coverage to make sure your CML still works as expected.  
  
I suppose allowing just "Ninja" is much more targeted than allowing any generator, but you'd still have to test -G Ninja under Linux and macOS using various versions of CMake.  
  
I did notice the other day that the target "boost_optional" was being built in one of the CI runs, even though Optional is header-only. Turns out this was caused by  
  
https://github.com/boostorg/optional/blob/bfdf0c73f98efba4c2458c806b1af9458e4bf79d/CMakeLists.txt#L14-L17  
  
so I suppose if nobody complains about Optional, we might start removing the generator check from our CMLs as well. :-)

---
