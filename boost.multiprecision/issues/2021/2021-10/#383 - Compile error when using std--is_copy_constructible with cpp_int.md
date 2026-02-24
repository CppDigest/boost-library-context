# #383 - Compile error when using std::is_copy_constructible with cpp_int [Closed]

> Username: tzubcic-tolar  
> Created at: 2021-10-19 08:04:35 UTC  
> Updated at: 2021-10-20 16:03:00 UTC  
> Closed at: 2021-10-20 16:03:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/383  

Happens with clang 11.0 and newer when using libstdc++. Example here: https://godbolt.org/z/bWxTaETdW  
  
I haven't digged much into the cause for this, but if it helps, user pdimov on cpplang slack suggested that this is probably some kind of constraint recursion and that `operator T()` needs an additional constraint that `T` is not `self_type`.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-10-19 12:18:08 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/383#issuecomment-946663930  

It's the -std=c++17 switch that causes the issue, and it's a recursive dependency on is_constructible: both is_copy_constructible and the conversion operator are implemented in terms of the same is_constructible template instantiation :(  
  
This might not be a quick fix, I think I need to write a traits class and gradually filter out the options through multiple layers.
