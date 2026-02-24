# #111 - Nonexistent Boost config macro BOOST_NO_CXX11_HDR_MEMORY [Closed]

> Username: jefftrull  
> Created at: 2020-09-04 05:01:37 UTC  
> Updated at: 2020-09-11 05:33:51 UTC  
> Closed at: 2020-09-11 05:33:51 UTC  
> Url: https://github.com/boostorg/wave/issues/111  

Two libraries, including Wave, erroneously use this macro, which never existed - the <memory> header preceded C++11.

---

## Comment 1

> Username: mclow  
> Created at: 2020-09-11 03:12:58 UTC  
> Url: https://github.com/boostorg/wave/issues/111#issuecomment-690847566  

Created https://github.com/boostorg/wave/pull/112 to address this.
