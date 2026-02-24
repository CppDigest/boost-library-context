# #6 [CMake] Generate cmake target that other libraries can use [Merged]

> Username: Mike-Devel  
> Created at: 2018-09-20 10:11:42 UTC  
> Updated at: 2019-01-16 18:20:12 UTC  
> Merged at: 2018-10-06 17:32:21 UTC  
> Closed at: 2018-10-06 17:32:21 UTC  
> Url: https://github.com/boostorg/static_assert/pull/6  

... to express their dependency on this library and retrieve any  
configuration information such as the include directory, binary  
to link to (if any), transitive dependencies, necessary compiler  
options or the required c++ standards level.

---

## Comment 1

> Username: Mike-Devel  
> Created_at: 2018-09-25 16:44:40 UTC  
> Url: https://github.com/boostorg/static_assert/pull/6#issuecomment-424417344  

Any comments on this?  
I don't claim to understand, why travis is failing for some jobs, but it definetively isn't due to this cmake file.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2018-09-25 17:16:40 UTC  
> Url: https://github.com/boostorg/static_assert/pull/6#issuecomment-424427700  

>I don't claim to understand, why travis is failing for some jobs, but it definetively isn't due to this cmake file.  
  
No it's a known clang-3.x issue that we have a workaround for in Boost.Config, but for some reason it's stopped working :(

---

## Comment 3

> Username: Mike-Devel  
> Created_at: 2018-10-01 08:44:28 UTC  
> Url: https://github.com/boostorg/static_assert/pull/6#issuecomment-425831880  

Rebased PR onto develop and added my alias in the copy right notice

---

## Comment 4

> Username: Mike-Devel  
> Created_at: 2018-10-08 08:05:25 UTC  
> Url: https://github.com/boostorg/static_assert/pull/6#issuecomment-427750491  

Thanks

---

## Comment 5

> Username: Lastique  
> Created_at: 2019-01-16 09:20:56 UTC  
> Url: https://github.com/boostorg/static_assert/pull/6#issuecomment-454708561  

Could it be merged to master, please?

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2019-01-16 18:20:12 UTC  
> Url: https://github.com/boostorg/static_assert/pull/6#issuecomment-454885030  

Done, thanks for the reminder.

---
