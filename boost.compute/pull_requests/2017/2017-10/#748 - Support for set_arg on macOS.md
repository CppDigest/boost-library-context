# #748 Support for set_arg on macOS [Merged]

> Username: henryiii  
> Created at: 2017-10-16 20:58:59 UTC  
> Updated at: 2019-02-10 15:29:26 UTC  
> Merged at: 2019-02-10 15:29:26 UTC  
> Closed at: 2019-02-10 15:29:26 UTC  
> Url: https://github.com/boostorg/compute/pull/748  

Currently, macOS does not support set_arg with some datatypes. This was partially fixed in #737, but it does not work if the user does not manually specify the `<boost/compute/types.hpp>` header manually.  
  
This PR is expected to fail to build on macOS on the first build. It will then need to be fixed.   
  
  
See ddemidov/vexcl#227 for more info.

---

## Comment 1

> Username: coveralls  
> Created_at: 2017-10-16 22:55:38 UTC  
> Url: https://github.com/boostorg/compute/pull/748#issuecomment-337067419  

[![Coverage Status](https://coveralls.io/builds/13744463/badge)](https://coveralls.io/builds/13744463)  
  
Coverage remained the same at 83.766% when pulling **c01065e5531da065af923c277b41f780843d289b on henryiii:add_test_mactypes** into **7daca6fc1ff289022c46fc6466565586f68f2244 on boostorg:develop**.

---

## Comment 2

> Username: ddemidov  
> Created_at: 2017-10-25 04:53:06 UTC  
> Url: https://github.com/boostorg/compute/pull/748#issuecomment-339213941  

@henryiii , can you try to add https://github.com/ddemidov/compute/commit/8f77efd0ef1aeeeed09fd097a675daa4eb9da644 to this PR? I believe that should fix the issue.

---

## Comment 3

> Username: coveralls  
> Created_at: 2017-10-27 11:01:16 UTC  
> Url: https://github.com/boostorg/compute/pull/748#issuecomment-339941418  

[![Coverage Status](https://coveralls.io/builds/13918807/badge)](https://coveralls.io/builds/13918807)  
  
Coverage remained the same at 83.766% when pulling **70aa256ec439c0b72738e8e9cbfc8279f5c128c7 on henryiii:add_test_mactypes** into **7daca6fc1ff289022c46fc6466565586f68f2244 on boostorg:develop**.

---

## Comment 4

> Username: ddemidov  
> Created_at: 2017-10-27 16:12:33 UTC  
> Url: https://github.com/boostorg/compute/pull/748#issuecomment-340016098  

I think the problem now is that [`test_kernels.cpp`](https://github.com/henryiii/compute/blob/70aa256ec439c0b72738e8e9cbfc8279f5c128c7/test/test_kernel.cpp#L14-L17) does not include `compute/core.hpp`. And we can not include `types.hpp` into the `kernel.hpp`, because `types.hpp` specializes some templates only declared in `kernel.hpp`. So may be the best solution for the failing test would be to either include `core.hpp` instead of `kernel.hpp`, or include `types.hpp` after `kernel.hpp`.

---

## Comment 5

> Username: henryiii  
> Created_at: 2017-10-27 16:44:48 UTC  
> Updated_at: 2017-10-27 16:46:01 UTC  
> Url: https://github.com/boostorg/compute/pull/748#issuecomment-340023732  

It bothers me that include order matters. Clang can even warn if includes are not sorted in alphabetical order.  
  
~~Couldn't `types` include `kernel`, since it works on the contents?~~  
  
Couldn't the types it specializes be declared first?

---

## Comment 6

> Username: coveralls  
> Created_at: 2017-10-28 05:12:06 UTC  
> Updated_at: 2019-02-10 15:10:39 UTC  
> Url: https://github.com/boostorg/compute/pull/748#issuecomment-340140042  

[![Coverage Status](https://coveralls.io/builds/21555484/badge)](https://coveralls.io/builds/21555484)  
  
Coverage remained the same at 84.02% when pulling **4ab09396638c8f0164c78bd21d5ab69e7f07e508 on henryiii:add_test_mactypes** into **827371af22eda723d68133ae48c8ef822cfbc209 on boostorg:develop**.

---

## Comment 7

> Username: henryiii  
> Created_at: 2018-05-18 15:41:44 UTC  
> Url: https://github.com/boostorg/compute/pull/748#issuecomment-390247815  

I believe this fix is okay to add.

---
