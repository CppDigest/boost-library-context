# #412 - Add buffers_body [Closed]

> Username: vinniefalco  
> Created at: 2017-06-02 20:19:21 UTC  
> Updated at: 2017-06-12 04:00:18 UTC  
> Closed at: 2017-06-12 04:00:18 UTC  
> Url: https://github.com/boostorg/beast/issues/412  

Pretty simple:  
```  
template<ConstBufferSequence>  
struct buffers_body  
{  
    struct value_type  
    {  
        boost::optional<ConstBuffersType> buffer;  
        bool more;  
    };  
  
    ...  
};  
```  
  
Needs tests and all that though. No rush.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-12 04:00:18 UTC  
> Url: https://github.com/boostorg/beast/issues/412#issuecomment-307685119  

This is merged as `buffer_body` !
