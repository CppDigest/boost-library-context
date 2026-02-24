# #88 - Provide a specialization of `is_result_type` with `std::expected` so that it could be used with `BOOST_LEAF_CHECK` and so on [Closed]

> Username: aandrejevas  
> Created at: 2026-02-17 10:15:46 UTC  
> Updated at: 2026-02-17 17:45:30 UTC  
> Closed at: 2026-02-17 17:45:29 UTC  
> Url: https://github.com/boostorg/leaf/issues/88  

Until something like and error propagation operator gets added to the language (https://github.com/cplusplus/papers/issues/1276), it would be convenient to use `BOOST_LEAF_CHECK` and other things with `std::expected` but now that is not possible because LEAF does not provide a `is_result_type` specialization with `std::expected`.

---

## Comment 1

> Username: zajo  
> Created at: 2026-02-17 17:45:29 UTC  
> Url: https://github.com/boostorg/leaf/issues/88#issuecomment-3916152817  

The only specialization of `is_result_type` provided by LEAF is for `leaf::result`. Users have to specialize the `is_result_type` template for any other type they want to use with LEAF error handling. Note that result types are required to be able to transport a `std::error_code`, so if you specialize `is_result_type` for `std::expected`, it has to be for `std::expected<T, std::error_code>`.
