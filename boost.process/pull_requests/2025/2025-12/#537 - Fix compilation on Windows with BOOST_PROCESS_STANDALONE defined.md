# #537 Fix compilation on Windows with BOOST_PROCESS_STANDALONE defined [Open]

> Username: jwhitworth-vicon  
> Created at: 2025-12-19 16:58:59 UTC  
> Updated at: 2026-02-11 02:50:29 UTC  
> Url: https://github.com/boostorg/process/pull/537  

While trying to use Boost.Process with the non-Boost standalone Asio, I found that some of the code in Boost.Process failed to compile when defining `BOOST_PROCESS_STANDALONE`, `BOOST_PROCESS_V2_STANDALONE` and `BOOST_PROCESS_USE_STD_FS`.  
  
These fixes address the issues raised in https://github.com/boostorg/process/issues/535.

---
