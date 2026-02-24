# #260 - A comment (not really an issue) [Closed]

> Username: rsn8887  
> Created at: 2019-07-13 02:34:05 UTC  
> Updated at: 2020-12-30 01:06:55 UTC  
> Closed at: 2020-12-30 01:06:54 UTC  
> Url: https://github.com/boostorg/asio/issues/260  

I just came across asio in a codebase I am working with.  
  
Here's an example codesnippet from the library (implementation inside a header file) that I came across:  
  
```  
template <typename SyncReadStream, typename DynamicBuffer>  
inline std::size_t read(SyncReadStream& s,  
    ASIO_MOVE_ARG(DynamicBuffer) buffers,  
    asio::error_code& ec,  
    typename enable_if<  
      is_dynamic_buffer<typename decay<DynamicBuffer>::type>::value  
    >::type*)  
{  
  return read(s, ASIO_MOVE_CAST(DynamicBuffer)(buffers),  
      transfer_all(), ec);  
}  
```  
  
Here's another one:  
```  
template <typename SyncReadStream, typename MutableBufferSequence>  
inline std::size_t read(SyncReadStream& s, const MutableBufferSequence& buffers,  
    typename enable_if<  
      is_mutable_buffer_sequence<MutableBufferSequence>::value  
    >::type*)  
{  
  asio::error_code ec;  
  std::size_t bytes_transferred = read(s, buffers, transfer_all(), ec);  
  asio::detail::throw_error(ec, "read");  
  return bytes_transferred;  
}  
```  
  
I find code such as this offensively unintelligible. How is anyone supposed to understand this, especially the signatures, let alone work with it? If this is how all C++ code looked, I would have never even started using this language. How could this type of stuff be endorsed by the C++ standard committee? If this is supposed to be a standard example of how everyone should code in C++, well, I think there's a very deep problem with the language itself, and/or its proponents.

---

## Comment 1

> Username: djarek  
> Created at: 2019-07-13 06:24:08 UTC  
> Url: https://github.com/boostorg/asio/issues/260#issuecomment-511093911  

I'm not sure what is the problem here - `enable_if`-based SFINAE is a standard C++ idiom, that has been around for about 20 years. The position is a bit awkward (i.e. an unused function argument), which I believe, due to compile-time performance differences (an unnamed template type parameter is slower to compile IIRC).  
  
Note that C++2a introduces syntax that is slightly easier to digest:  
```  
template <std::net::SyncReadStream S, std::net::MutableBufferSequence B>  
inline std::size_t read(S& s, const B& buffers);  
```  
  
As for why the committee would endorse it - I suggest you look into a standard library implementation, you'll be surprised how unintelligible some things are there.

---

## Comment 2

> Username: tanzislam  
> Created at: 2019-08-10 19:55:23 UTC  
> Url: https://github.com/boostorg/asio/issues/260#issuecomment-520175508  

The `typename decay<DynamicBuffer>::type` bit in the first snippet can probably be changed to `decay_t<DynamicBuffer>`, at the expense of requiring C++14.

---

## Comment 3

> Username: ghost  
> Created at: 2020-12-30 01:06:53 UTC  
> Url: https://github.com/boostorg/asio/issues/260#issuecomment-752292528  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#704](https://github.com/chriskohlhoff/asio/issues/704).
