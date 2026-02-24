# #536 - Dead loop on aarch64 platform [Open]

> Username: chestnutprog  
> Created at: 2025-12-19 16:08:16 UTC  
> Updated at: 2026-01-10 22:41:20 UTC  
> Url: https://github.com/boostorg/process/issues/536  

There are some tautological-constant-out-of-range-compare warnings on aarch64 platform, because `char` is an unsigned type on it. `src/ext/cmd.cpp` is fine because result is still correct in this situation, but `src/ext/env.cpp` doesn't work well.  
  
```  
external/boost/libs/process/src/ext/cmd.cpp:222:28: error: result of comparison of constant -1 with expression of type '__gnu_cxx::__alloc_traits<std::allocator<char>, char>::value_type' (aka 'char') is always true [-Werror,-Wtautological-constant-out-of-range-compare]  
    while (procargs.back() != EOF)  
           ~~~~~~~~~~~~~~~ ^  ~~~  
external/boost/libs/process/src/ext/cmd.cpp:240:25: error: result of comparison of constant -1 with expression of type '__gnu_cxx::__alloc_traits<std::allocator<char>, char>::value_type' (aka 'char') is always false [-Werror,-Wtautological-constant-out-of-range-compare]  
    if (procargs.back() == EOF)  
        ~~~~~~~~~~~~~~~ ^  ~~~  
  
external/boost/libs/process/src/ext/env.cpp:130:16: error: result of comparison of constant -1 with expression of type 'char' is always true [-Werror,-Wtautological-constant-out-of-range-compare]  
    while (*nh != EOF)  
           ~~~ ^  ~~~  
external/boost/libs/process/src/ext/env.cpp:321:50: error: result of comparison of constant -1 with expression of type 'char' is always true [-Werror,-Wtautological-constant-out-of-range-compare]  
    while (!procargs || procargs.get()[size - 1] != EOF)  
                        ~~~~~~~~~~~~~~~~~~~~~~~~ ^  ~~~  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-12-19 16:16:38 UTC  
> Url: https://github.com/boostorg/process/issues/536#issuecomment-3675649356  

Is the fix to just cast `EOF` to char?

---

## Comment 2

> Username: chestnutprog  
> Created at: 2025-12-19 16:36:09 UTC  
> Updated at: 2025-12-19 16:37:55 UTC  
> Url: https://github.com/boostorg/process/issues/536#issuecomment-3675738149  

> Is the fix to just cast `EOF` to char?  
  
I think it should be able to resolve the warning and dead loop. But I just noticed that Linux environment variables can actually include 0xff, so is it a more appropriate approach to record the actual length and use the length for judgment? I'm willing to submit a PR for this.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2026-01-10 22:41:20 UTC  
> Url: https://github.com/boostorg/process/issues/536#issuecomment-3733638308  

If it's possible to get the actual length, sure.
