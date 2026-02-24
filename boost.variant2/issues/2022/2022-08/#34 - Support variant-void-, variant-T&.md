# #34 - Support variant<void>, variant<T&> [Open]

> Username: pdimov  
> Created at: 2022-08-03 07:30:18 UTC  
> Updated at: 2024-12-18 09:14:36 UTC  
> Url: https://github.com/boostorg/variant2/issues/34  

This as a side effect will make it possible to remove the `result<void>` specialization, and will obviate the need for a `result<T&>` specialization.  
  
`void` will need to be replaced in `variant_base` with `variant2::void_`, and `T&` - with either `std::reference_wrapper<T>` or `variant2::detail::refwrap<T>`.

---

## Comment 1

> Username: Lastique  
> Created at: 2024-12-18 09:14:35 UTC  
> Url: https://github.com/boostorg/variant2/issues/34#issuecomment-2550774832  

The support for references is also requested in https://github.com/boostorg/variant2/issues/5.
