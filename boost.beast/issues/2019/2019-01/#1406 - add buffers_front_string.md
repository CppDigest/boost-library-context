# #1406 - add buffers_front_string [Closed]

> Username: vinniefalco  
> Created at: 2019-01-07 13:52:33 UTC  
> Updated at: 2022-06-16 16:22:33 UTC  
> Closed at: 2022-06-16 16:22:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1406  

Returns the first buffer in a buffer sequence as a `string_view`

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-01-07 13:54:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1406#issuecomment-451941693  

```  
/// Returns the first buffer in a sequence as a string view.  
template<class ConstBufferSequence>  
std::string_view  
buffers_front_string(ConstBufferSequence const& buffers)  
{  
    auto const b = beast::buffers_front(buffers);  
    return {reinterpret_cast<char const*>(b.data()), b.size()};  
}  
```

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-03-08 21:00:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1406#issuecomment-471074793  

We should also add these:  
```  
string_view basic_flat_buffer::str() const noexcept  
string_view basic_flat_static_buffer::str() const noexcept  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-06-16 16:22:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1406#issuecomment-1157871117  

We aren't doing this anymore
