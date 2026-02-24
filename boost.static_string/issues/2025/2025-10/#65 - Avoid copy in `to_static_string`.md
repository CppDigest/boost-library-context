# #65 - Avoid copy in `to_static_string` [Closed]

> Username: Flamefire  
> Created at: 2025-10-01 11:46:06 UTC  
> Updated at: 2025-10-21 16:36:08 UTC  
> Closed at: 2025-10-21 16:36:08 UTC  
> Url: https://github.com/boostorg/static_string/issues/65  

Those functions do a `snprintf` to a temporary buffer and copy that to the result.  
  
Instead the buffer of the resulting static_string could be used directly

---

## Comment 1

> Username: gennaroprota  
> Created at: 2025-10-02 10:07:17 UTC  
> Updated at: 2025-10-03 07:59:51 UTC  
> Url: https://github.com/boostorg/static_string/issues/65#issuecomment-3360287096  

Those functions generate the digits in reverse order, and don't know in advance how many digits will be generated, so they start writing from `*--end()`. If one does the same into the internal buffer of `static_string`, then characters must be finally moved to the beginning; something like this:  
  
```  
template< std::size_t N, typename Integer >  
inline  
static_string< N >  
to_static_string_int_impl( Integer value ) noexcept  
{  
  static_string< N > str;  
  str.set_size( N );  
  const auto digits_end = str.end();  
  const auto digits_begin = integer_to_string< std::char_traits< char >, Integer >(  
    digits_end, value, std::is_signed< Integer >{} );  
  str.set_size( static_cast< std::size_t >( digits_end - digits_begin ) );  
  std::memmove( str.data(), digits_begin, str.size() );  
  return str;  
}  
```  
  
Is that what you have in mind?

---

## Comment 2

> Username: Flamefire  
> Created at: 2025-10-02 11:46:16 UTC  
> Url: https://github.com/boostorg/static_string/issues/65#issuecomment-3360784668  

I was looking at the float impls which is basically:  
  
```  
  char buffer[N + 1];  
  std::snprintf(buffer, N + 1, "%f", value)  
  return static_string<N>(buffer);  
```  
For the int conversion with that reverse-writing I guess `memcpy` would be faster than the `memmove`  
  
However currently it ends up calling `traits_type::move`, i.e. memmove at which point you could do what you suggested above directly which might be better as omitting the buffer could lead to less cache contention. If the optimizer isn't smart enough to elide it anyway.     
Or do a direct `memcpy` in `to_static_string_int_impl` from the buffer to `data()`

---

## Comment 3

> Username: gennaroprota  
> Created at: 2025-10-21 16:36:08 UTC  
> Url: https://github.com/boostorg/static_string/issues/65#issuecomment-3427586286  

Fixed with cd1a1a41c39888c74a0b1e228508e95048d92e5f.
