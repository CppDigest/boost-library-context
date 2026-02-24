# #58 Add a note to mp_for_each for lists with elements which are not default-constructible [Merged]

> Username: bernhardmgruber  
> Created at: 2021-03-04 11:52:28 UTC  
> Updated at: 2021-03-04 19:02:56 UTC  
> Merged at: 2021-03-04 18:12:23 UTC  
> Closed at: 2021-03-04 18:12:23 UTC  
> Url: https://github.com/boostorg/mp11/pull/58  

I ran into an issue with `mp_for_each`: My type list contained a `std::vector` but I was calling the `mp_for_each` in a `constexpr` context, compiling with C++17, which tried to instantiate the `std::vector` which is not `constexpr`.  
  
Using `mp_for_each<mp_transform<mp_identity, L>>` as described in this issue was the perfect solution: https://github.com/boostorg/mp11/issues/23  
  
Please add a hint for this to the documentation! Thank you!

---
