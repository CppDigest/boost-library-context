# #215 - Support binding containers in BOOST_COMPUTE_CLOSURE() [Closed]

> Username: kylelutz  
> Created at: 2014-08-07 02:38:36 UTC  
> Updated at: 2014-08-09 16:08:40 UTC  
> Closed at: 2014-08-09 16:08:40 UTC  
> Url: https://github.com/boostorg/compute/issues/215  

The `BOOST_COMPUTE_CLOSURE()` macro should support binding more than just simple data types. It should support binding container types (such as `vector<T>` and `array<T, N>`) and make them accessible to the kernel code.  
  
See this thread on the mailing-list: https://groups.google.com/forum/?utm_medium=email&utm_source=footer#!msg/boost-compute/dOX9xB4oNYQ/mpaYgVORfAQJ

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-08-08 05:15:39 UTC  
> Url: https://github.com/boostorg/compute/issues/215#issuecomment-51564272  

Implemented in PR #224
