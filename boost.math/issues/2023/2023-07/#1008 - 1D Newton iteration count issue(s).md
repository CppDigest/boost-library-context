# #1008 - 1D Newton iteration count issue(s) [Open]

> Username: ryanelandt  
> Created at: 2023-07-31 18:20:05 UTC  
> Updated at: 2023-07-31 19:21:29 UTC  
> Url: https://github.com/boostorg/math/issues/1008  

The documentation for `newton_raphson_iterate` says:  
```  
uintmax_t& max_iter  
    An optional maximum number of iterations to perform.  
    On exit, this is updated to the actual number of iterations performed.   
```  
There are two issues relating to `max_iter`:  
1. `max_iter` is copied to a variable `count`. After each Newton iteration, one is subtracted from `count`. Because of the `do {...} while (count && ...)` structure of the Newton iteration, if `max_iter = 0`, rollover occurs, making `max_iter` functionally infinite.  
4. If `0 < max_iter`, running out of iterations exits the Newton loop as if the tolerance was met.  
  
These two observations are demonstrated in the code below.  
  
```c++  
const auto fn = [](double x) { return std::make_pair(x * x - 3, 2 * x); };  
std::uintmax_t iters;  
std::cout << std::setprecision(16);  
  
for (int count = 0; count < 10; count++) {  
   iters = count;  
   const double x0 = boost::math::tools::newton_raphson_iterate(fn, 10.0, 0.0, 100.0, 52, iters);  
   std::cout << "max_iters: " << count << " -- used_iters: " << iters << " -- x: " << x0 << std::endl;  
}  
```  
  
```  
max_iters: 0 -- used_iters: 8 -- x: 1.732050807568877  
max_iters: 1 -- used_iters: 1 -- x: 5.15  
max_iters: 2 -- used_iters: 2 -- x: 2.86626213592233  
max_iters: 3 -- used_iters: 3 -- x: 1.956460731776899  
max_iters: 4 -- used_iters: 4 -- x: 1.74492093914502  
max_iters: 5 -- used_iters: 5 -- x: 1.732098271119538  
max_iters: 6 -- used_iters: 6 -- x: 1.732050808219183  
max_iters: 7 -- used_iters: 7 -- x: 1.732050807568877  
max_iters: 8 -- used_iters: 8 -- x: 1.732050807568877  
max_iters: 9 -- used_iters: 8 -- x: 1.732050807568877  
```

---

## Comment 1

> Username: ryanelandt  
> Created at: 2023-07-31 18:24:18 UTC  
> Updated at: 2023-07-31 19:21:29 UTC  
> Url: https://github.com/boostorg/math/issues/1008#issuecomment-1658925633  

Because of the issue described above, the unit test https://github.com/boostorg/math/blob/50ef83a47b13c02bf9d6c0e86f9066c431ac42cb/test/test_roots.cpp#L657-L659 passes, even though the answer in produces in inaccurate due to running out of iterations.
