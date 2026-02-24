# #446 features: Add freertos as an operating system. [Merged]

> Username: tee3  
> Created at: 2019-05-29 13:30:07 UTC  
> Updated at: 2021-10-02 20:59:35 UTC  
> Merged at: 2019-06-03 19:53:49 UTC  
> Closed at: 2019-06-03 19:53:49 UTC  
> Url: https://github.com/boostorg/build/pull/446  

This pull request adds FreeRTOS (`freetos`) to the list of operating systems, which has the result of adding it to the available operating systems in the `target-os` feature.  
  
This is not (yet) used anywhere in Boost.Build and so is just useful for users of Boost.Build that might want to build something for FreeRTOS.  I'm not sure much could be done without providing a `freertos` module since FreeRTOS is pretty configurable and would be hard to generalize for a specific toolset such as `gcc`.  I may try to add a module for FreeRTOS sometime later.  
  
I didn't see any tests for specific target operating systems (e.g. `elf` for bare-metal systems), so I didn't try to add any here.  
  
I'm using this right now while developing a generator for the Xilinx SDK, which can generate a BSP for a bare-metal system or a FreeRTOS system for various processors.

---

## Comment 1

> Username: tee3  
> Created_at: 2019-05-29 17:52:51 UTC  
> Url: https://github.com/boostorg/build/pull/446#issuecomment-497042175  

Sorry about the rebase, I had committed using the wrong author.

---

## Comment 2

> Username: github-actions[bot]  
> Created_at: 2021-10-02 20:59:34 UTC  
> Url: https://github.com/boostorg/build/pull/446#issuecomment-932819667  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
