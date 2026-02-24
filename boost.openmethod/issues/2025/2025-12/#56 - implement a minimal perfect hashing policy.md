# #56 - implement a minimal perfect hashing policy [Open]

> Username: jll63  
> Created at: 2025-12-18 01:14:13 UTC  
> Updated at: 2025-12-18 04:37:34 UTC  
> Url: https://github.com/boostorg/openmethod/issues/56  

Implement a new `type_hash` policy, `minimal_perfect_hash`, that is modeled after the existing `fast_perfect_hash` policy, except that it finds a perfect minimal hash function using the PtHash algorithm. The primary hash function is in the form `H(x) = (M * x) >> N`, where `x` is the value to hash (a `type_id`), and M and N are the integer parameters of the hash function. Like with `fast_perfect_hash`, if the `runtime_checks` policy is present in the registry, create a control table during initialization, and use it during hashing to check that the input value `x` is in the universe passed to `initialize`. Also create a test suite.
