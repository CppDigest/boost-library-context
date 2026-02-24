# #181 Match `std::integral_constant` by adding `operator()` and by marking `operator T` as `noexcept`. Other minor changes. [Closed]

> Username: DoctorNoobingstoneIPresume  
> Created at: 2023-06-13 20:36:20 UTC  
> Updated at: 2025-04-21 14:19:43 UTC  
> Closed at: 2025-04-21 14:19:43 UTC  
> Url: https://github.com/boostorg/type_traits/pull/181  

Conversion between pointers of unrelated types `Source` and `Target` can be done with `static_cast` => IMO should be done with `static_cast` (in order not to give the impression that `reinterpret_cast` really is needed):  
  
  `T *ptr_target = static_cast <T *> (static_cast <void *> (ptr_source));`  
  
IMO `reinterpret_cast` should be used for pointer-to-integral and integral-to-pointer conversions (only).

---

## Comment 1

> Username: DoctorNoobingstoneIPresume  
> Created_at: 2025-03-16 02:51:13 UTC  
> Url: https://github.com/boostorg/type_traits/pull/181#issuecomment-2727150371  

@jzmaddock @igaztanaga @glenfe @viboes  
https://youtu.be/evKAKb8iDv4 🤔

---
