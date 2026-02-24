# #5 - (non)member_ccs(_exact) fail on gcc 4.8, clang 3.9 [Open]

> Username: pdimov  
> Created at: 2017-10-13 00:09:24 UTC  
> Updated at: 2017-10-13 01:35:05 UTC  
> Url: https://github.com/boostorg/function_types/issues/5  

See f.ex. here: https://travis-ci.org/boostorg/boost/builds/285667073

---

## Comment 1

> Username: eldiener  
> Created at: 2017-10-13 01:35:05 UTC  
> Url: https://github.com/boostorg/function_types/issues/5#issuecomment-336326115  

I am aware of this failure. I am looking into removing the build for the custom calling convention tests when the particular compiler does not support the required custom calling convention. It does look like the particular Travis CI tests should succeed if the compilation is 32-bit ( __i386__ is defined under gcc ), so I am at a loss trying to understand why the particular tests fail. But in general the only way of removing this particular failure is to not perform the custom calling conventions tests when they are inappropriate for the compiler.
