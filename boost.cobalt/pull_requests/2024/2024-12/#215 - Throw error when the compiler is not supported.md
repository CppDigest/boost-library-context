# #215 Throw error when the compiler is not supported [Closed]

> Username: JackBoosY  
> Created at: 2024-12-11 06:34:21 UTC  
> Updated at: 2024-12-16 05:51:19 UTC  
> Closed at: 2024-12-13 08:59:41 UTC  
> Url: https://github.com/boostorg/cobalt/pull/215  

The current behavior will mislead the user into thinking that configuration was successful, when in fact it failed due to unsupported compiler version.

---

## Comment 1

> Username: JackBoosY  
> Created_at: 2024-12-11 06:35:15 UTC  
> Url: https://github.com/boostorg/cobalt/pull/215#issuecomment-2533755748  

Ref:  
https://github.com/microsoft/vcpkg/issues/40290  
https://github.com/microsoft/vcpkg/issues/40310  
https://github.com/microsoft/vcpkg/issues/39573  
https://github.com/microsoft/vcpkg/issues/39495  
https://github.com/microsoft/vcpkg/issues/38498

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2024-12-11 16:55:35 UTC  
> Url: https://github.com/boostorg/cobalt/pull/215#issuecomment-2536547754  

That's not a solution, that would break any cmake build on compilers that don't support C++20.  
The configuration is fine as is, you can build boost, just without cobalt. This requires a fix on vcpkg.

---

## Comment 3

> Username: JackBoosY  
> Created_at: 2024-12-11 23:20:45 UTC  
> Url: https://github.com/boostorg/cobalt/pull/215#issuecomment-2537401729  

> That's not a solution, that would break any cmake build on compilers that don't support C++20. The configuration is fine as is, you can build boost, just without cobalt. This requires a fix on vcpkg.  
  
If I understand correctly, the configuration step will break when the compiler doesn't support cxx 20, isn't it?

---

## Comment 4

> Username: klemens-morgenstern  
> Created_at: 2024-12-12 00:50:19 UTC  
> Url: https://github.com/boostorg/cobalt/pull/215#issuecomment-2537505549  

Correct.

---

## Comment 5

> Username: JackBoosY  
> Created_at: 2024-12-12 01:51:12 UTC  
> Updated_at: 2024-12-12 01:52:47 UTC  
> Url: https://github.com/boostorg/cobalt/pull/215#issuecomment-2537572893  

> Correct.  
  
So I don't know why this changes is incorrect.  
If the compiler doesn't meet the requirements, it should be FAILED at configure step, not just return and pass.  
  
This affects not only vcpkg, but all steps using the command line, as they will collect the results of whether the configuration passed or not.

---

## Comment 6

> Username: klemens-morgenstern  
> Created_at: 2024-12-12 03:00:56 UTC  
> Url: https://github.com/boostorg/cobalt/pull/215#issuecomment-2537689057  

Because this is built as part of boost. Your change would make configuring boost fail if the compiler isn't supported.

---

## Comment 7

> Username: JackBoosY  
> Created_at: 2024-12-13 06:46:09 UTC  
> Url: https://github.com/boostorg/cobalt/pull/215#issuecomment-2540667511  

> Because this is built as part of boost. Your change would make configuring boost fail if the compiler isn't supported.  
  
So I assume this approach is flawed? If the user builds boost with a compiler that doesn't meet the requirements, the "correct" results obtained may be different from what is expected and the results can only be confirmed by the build log?

---

## Comment 8

> Username: klemens-morgenstern  
> Created_at: 2024-12-13 08:59:41 UTC  
> Url: https://github.com/boostorg/cobalt/pull/215#issuecomment-2540918123  

You assume wrongly. Boost is a library collection that has different requirements, such as C++20 for boost.cobalt.  
  
Your proposed change is not a solution.

---
