# #218 fix is_buffer_sequence [Closed]

> Username: huangqinjin  
> Created at: 2019-04-03 20:01:34 UTC  
> Updated at: 2020-12-30 15:25:01 UTC  
> Closed at: 2020-12-30 01:56:30 UTC  
> Url: https://github.com/boostorg/asio/pull/218  

Test case:  
```  
using namespace boost::asio;  
  
struct no_buffers  
{  
    typedef const_buffer value_type;  
    typedef const_buffer* const_iterator;  
    const_iterator begin();  
};  
  
struct buffers : public no_buffers  
{  
    const_iterator end();  
};  
  
#ifndef BOOST_ASIO_NO_DEPRECATED  
static_assert(is_const_buffer_sequence<const_buffers_1>::value == 1, "");  
#endif  
  
static_assert(is_const_buffer_sequence<no_buffers>::value == 0, "");  
static_assert(is_const_buffer_sequence<buffers>::value == 1, "");  
```

---

## Comment 1

> Username: karzhenkov  
> Created_at: 2019-07-03 16:58:21 UTC  
> Url: https://github.com/boostorg/asio/pull/218#issuecomment-508174954  

See chriskohlhoff/asio#402, chriskohlhoff/asio#403

---

## Comment 2

> Username: karzhenkov  
> Created_at: 2020-01-05 19:32:42 UTC  
> Url: https://github.com/boostorg/asio/pull/218#issuecomment-570940277  

Applied in 6f24e3d52d79de9e494314ec20e2921ed408f39d,  chriskohlhoff/asio@734e339d3f3beb8243614a8dd950bc4144ea94ab.

---

## Comment 3

> Username: chriskohlhoff  
> Created_at: 2020-12-30 01:56:30 UTC  
> Url: https://github.com/boostorg/asio/pull/218#issuecomment-752299972  

Fixed in asio 1.14.1 / boost 1.71.

---
