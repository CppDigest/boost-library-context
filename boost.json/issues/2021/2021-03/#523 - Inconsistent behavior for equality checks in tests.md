# #523 - Inconsistent behavior for equality checks in tests [Open]

> Username: doganulus  
> Created at: 2021-03-07 09:48:20 UTC  
> Updated at: 2021-03-08 15:49:21 UTC  
> Url: https://github.com/boostorg/json/issues/523  

Currently some test cases use a helper `equal` function defined in `test/test.hpp` to check the equality between `json::value`s.   
  
https://github.com/boostorg/json/blob/ee8d72d8502b409b5561200299cad30ccdb91415/test/test.hpp#L971-L1054  
  
Its behavior is inconsistent with the `operator==` of `json::value` as it distinguishes the cases of `int64` and `uint64`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-03-07 15:30:30 UTC  
> Updated at: 2021-03-07 15:30:59 UTC  
> Url: https://github.com/boostorg/json/issues/523#issuecomment-792297021  

The point of `equal` in the test is to check for bitwise equality. This is a different use-case than `operator==`. Otherwise, the tests would just use `==` instead of calling `equal` :)

---

## Comment 2

> Username: doganulus  
> Created at: 2021-03-07 19:53:28 UTC  
> Url: https://github.com/boostorg/json/issues/523#issuecomment-792341481  

Ah equality over wires... ok, got it.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-03-07 21:35:55 UTC  
> Url: https://github.com/boostorg/json/issues/523#issuecomment-792357529  

> Ah equality over wires  
  
Not exactly "wire" but rather, that the tests expect a bitwise equality instead of a semantic equality (which could invalidate the test). In other words this version of `equal` is specifically for helping the tests ensure that the correct bit-patterns are produced. We could rename this if it would help (`bitwise_equal`?)

---

## Comment 4

> Username: doganulus  
> Created at: 2021-03-08 07:32:16 UTC  
> Url: https://github.com/boostorg/json/issues/523#issuecomment-792539606  

Yes, renaming the helper function would prevent the confusion.
