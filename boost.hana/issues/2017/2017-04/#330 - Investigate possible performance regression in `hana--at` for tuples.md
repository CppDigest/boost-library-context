# #330 - Investigate possible performance regression in `hana::at` for tuples [Open]

> Username: ldionne  
> Created at: 2017-04-06 19:07:40 UTC  
> Updated at: 2019-01-05 06:22:32 UTC  
> Url: https://github.com/boostorg/hana/issues/330  

`hana::at` for tuples seem to be slower than `hana::at_key` for maps on http://metaben.ch. See [this](http://metaben.ch/hetero/clang++-3.9/at/index.html) and [this](http://metaben.ch/hetero/clang++-3.9/at_key/index.html). Is this a genuine bug, or something else? This could have to do with the "improved" support for EBO in tuple introduced in https://github.com/boostorg/hana/pull/313.

---

## Comment 1

> Username: ricejasonf  
> Created at: 2019-01-05 06:22:32 UTC  
> Url: https://github.com/boostorg/hana/issues/330#issuecomment-451631836  

The `p` dimension for the test on `at` is much larger (15:100) which probably explains why it appears slower.  
  
Here are the specs from the `CMakeLists.txt` files:  
`add_dataset(datasets hetero at_key hana map hana.map.cpp.erb 1 "(0..50).step(10).to_a + (100..300).step(50).to_a" "p\;15")`  
  
`add_dataset(datasets hetero at hana tuple hana.tuple.cpp.erb 3 "(0..50).step(10).to_a + (100..300).step(50).to_a" "p\;100")`  
  
Completely orthogonal to this issue, but related to the performance of `at`, I did a benchmark against my implementation of *Parametric Expressions* (P1221).  
  
![screen shot 2019-01-04 at 18 59 08](https://user-images.githubusercontent.com/2257044/50721025-187f8200-106d-11e9-86f5-26b34511a1c8.png)  
  
In addition to using parmexprs, I also bypassed `ebo_get` by having a member type alias within `detail::basic_tuple_impl`:  
```cpp  
template <std::size_t i>  
using ebo_t = ebo<bti<i>, __type_pack_element<i, Xn...>>;  
```  
https://github.com/ricejasonf/hana/blob/56605956152066ece1097e9ffae5eabd7610e6be/include/parmexpr/hana/basic_tuple.hpp#L187  
  
That's 64% faster with the largest tuple case. I bet the difference would be even larger if the elements themselves were not small type names (like tuple of tuples).
