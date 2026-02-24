# #70 - boost::asio::buffer includes null character when used with string literal [Closed]

> Username: ghost  
> Created at: 2017-11-27 19:22:37 UTC  
> Updated at: 2017-12-02 08:12:08 UTC  
> Closed at: 2017-12-02 08:12:08 UTC  
> Url: https://github.com/boostorg/asio/issues/70  

When using boost::asio::buffer with a string literal, the buffer includes the terminating null character, which is not what one would expect.  
  
This could be solved with something like this:  
  
``` cpp  
/// Create a new non-modifiable buffer that represents the given character array.  
/**  
  * @returns A const_buffer value equivalent to:  
  * @code const_buffer(  
  *     static_cast<const void*>(data),  
  *     N * sizeof(char)); @endcode  
  */  
template <std::size_t N>  
inline BOOST_ASIO_CONST_BUFFER buffer(  
    const char (&data)[N]) BOOST_ASIO_NOEXCEPT  
{  
  return BOOST_ASIO_CONST_BUFFER(data, (N - 1) * sizeof(char));  
}  
```  
  
But this might cause trouble when using a const char array that is not null-terminated, since we then basically drop the last character.

---

## Comment 1

> Username: chriskohlhoff  
> Created at: 2017-12-02 08:12:08 UTC  
> Url: https://github.com/boostorg/asio/issues/70#issuecomment-348676529  

Won't fix. This will break code, as this overload is used for arrays that are not string literals.
