# #126 - Typo in example? [Open]

> Username: reddwarf69  
> Created at: 2022-09-28 14:06:46 UTC  
> Updated at: 2022-09-28 14:06:46 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/126  

In https://github.com/boostorg/safe_numerics/blob/13ca3d6dd36db1aac2d6b5caca2c281d15c881ad/example/example84.cpp#L37 it says "we know that this cannot fail".  
  
The example, before, has defined a safe_t and input_safe_t types... which are identical. I suspect the intention was to use loose_trap_policy in https://github.com/boostorg/safe_numerics/blob/13ca3d6dd36db1aac2d6b5caca2c281d15c881ad/example/example84.cpp#L24.
