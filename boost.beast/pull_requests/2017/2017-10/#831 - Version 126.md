# #831 Version 126 [Merged]

> Username: vinniefalco  
> Created at: 2017-10-24 20:33:13 UTC  
> Updated at: 2017-10-29 16:29:03 UTC  
> Merged at: 2017-10-24 22:25:12 UTC  
> Closed at: 2017-10-24 22:25:12 UTC  
> Url: https://github.com/boostorg/beast/pull/831  

* Add CppCon2017 presentation link  
* Update README.md  
* Update stream write documentation for end of stream changes  
* Tidy up unused variable warnings  
* Don't return end_of_stream on win32 file body writes  
* Fix doc typo  
* Fix shadowing in session_alloc  
* Fix executor type compilation  
* Add Travis tests with the default compilers  
* Update Boost.WinAPI usage to the new location and namespace.  
* Fix buffered_read_stream async_read_some signature

---

## Review 1 [Commented]

> Username: djarek  
> Created_at: 2017-10-24 21:18:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/831#pullrequestreview-71674777  

📁 include/boost/beast/core/impl/buffered_read_stream.ipp

```diff
 146 | template<class ConstBufferSequence, class WriteHandler>
 145 |- auto
 147 |+ BOOST_ASIO_INITFN_RESULT_TYPE(WriteHandler, void(error_code))
```

> Username: djarek  
> Created_at: 2017-10-24 21:13:12 UTC  
> Updated_at: 2017-10-24 21:26:16 UTC  
> Url: https://github.com/boostorg/beast/pull/831#discussion_r146694250  

The signature of an async_write_some handler of an AysncWriteStream should be void(error_code,size_t).


---
