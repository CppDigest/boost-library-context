# #448 - Add static_buffer_n::base [Closed]

> Username: vinniefalco  
> Created at: 2017-06-09 02:19:39 UTC  
> Updated at: 2017-08-04 23:08:48 UTC  
> Closed at: 2017-08-04 23:08:48 UTC  
> Url: https://github.com/boostorg/beast/issues/448  

To eliminate unnecessary template instantiations for different sized buffers:  
```  
template<std::size_t N>  
static_buffer&  
static_buffer_n<N>::  
base()  
{  
  return *this;  
}  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-04 02:09:36 UTC  
> Url: https://github.com/boostorg/beast/issues/448#issuecomment-312763518  

Renamed to `base`

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-07-04 02:59:29 UTC  
> Url: https://github.com/boostorg/beast/issues/448#issuecomment-312768997  

Should we rename it to `static_buffer_storage`

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-08-04 23:08:48 UTC  
> Url: https://github.com/boostorg/beast/issues/448#issuecomment-320371956  

All taken care of
