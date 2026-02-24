# #156 - std::bfloat16_t and std::float16_t to_chars output exceeds type precision [Closed]

> Username: mborland  
> Created at: 2024-02-06 10:51:37 UTC  
> Updated at: 2024-04-16 05:59:43 UTC  
> Closed at: 2024-04-16 05:59:43 UTC  
> Url: https://github.com/boostorg/charconv/issues/156  

Since `std::bfloat16_t` uses `float` as an interchange type the result can exceed the length of brainfloat. The value of `std::numeric_limits<std::bfloat16_t>::max_digits10` is 4 so output should be capped at that.

---

## Comment 1

> Username: mborland  
> Created at: 2024-02-12 09:56:50 UTC  
> Url: https://github.com/boostorg/charconv/issues/156#issuecomment-1938351407  

Can better fix this by using conversion to float when the precision is specified, or ryu generic when the precision is unspecified so that we don't end up with unnecessary trailing zeros.
