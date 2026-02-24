# #76 - record_ostream.cpp does not respect BOOST_LOG_USE_COMPILER_TLS [Closed]

> Username: justinasvd  
> Created at: 2019-02-07 12:19:26 UTC  
> Updated at: 2019-02-08 14:33:28 UTC  
> Closed at: 2019-02-07 17:02:21 UTC  
> Url: https://github.com/boostorg/log/issues/76  

Linker output  
```  
./3rdparty/boost/stage/lib/libboost_log.a(record_ostream.o): In function `boost::thread_specific_ptr<boost::log::v2s_mt_posix::aux::(anonymous namespace)::stream_compound_pool<char> >::~thread_specific_ptr()':  
record_ostream.cpp:(.text+0x1b5): undefined reference to `boost::detail::set_tss_data(void const*, boost::shared_ptr<boost::detail::tss_cleanup_function>, void*, bool)'  
../3rdparty/boost/stage/lib/libboost_log.a(record_ostream.o): In function `boost::thread_specific_ptr<boost::log::v2s_mt_posix::aux::(anonymous namespace)::stream_compound_pool<wchar_t> >::~thread_specific_ptr()':  
record_ostream.cpp:(.text+0x2a5): undefined reference to `boost::detail::set_tss_data(void const*, boost::shared_ptr<boost::detail::tss_cleanup_function>, void*, bool)'  
../3rdparty/boost/stage/lib/libboost_log.a(record_ostream.o): In function `boost::log::v2s_mt_posix::aux::stream_provider<char>::release_compound(boost::log::v2s_mt_posix::aux::stream_provider<char>::stream_compound*)':  
...  
```  
indicates a dependency on Boost.Thread.  
  
Expected result:  
Boost.Log compiled with BOOST_LOG_USE_COMPILER_TLS does not have a dependency on Boost.Thread.

---

## Comment 1

> Username: Lastique  
> Created at: 2019-02-07 17:02:21 UTC  
> Url: https://github.com/boostorg/log/issues/76#issuecomment-461511822  

Defining `BOOST_LOG_USE_COMPILER_TLS` does not remove the dependency on Boost.Thread. Compiler-specific TLS is used for caching, the actual value is still managed by Boost.Thread. There are also other parts of Boost.Thread used in Boost.Log, besides TLS.

---

## Comment 2

> Username: justinasvd  
> Created at: 2019-02-08 09:44:04 UTC  
> Updated at: 2019-02-08 09:46:53 UTC  
> Url: https://github.com/boostorg/log/issues/76#issuecomment-461746180  

Fair enough. Then this should probably be reflected in the documentation. Otherwise, it's all too easy to get an impression that this would use some kind of compiler intrinsic, and won't use Boost.Thread.  
  
That being said, are there any plans to use C++11 threading primitives (`thread_local` storage, mutexes, etc.) when there is C++11 support? Much like was done with `std::regex`.  
  
You see, I am asking not only because I want to minimize code bloat in our software (where we use Boost.Log, by the way), but also because the strain to maintain uniform build environments for Linux, MinGW cross-compiling, etc. increases with any additional library. For instance, when compilation for Linux mostly works out of the box, compilation with MXE is much less straightforward.

---

## Comment 3

> Username: Lastique  
> Created at: 2019-02-08 13:32:11 UTC  
> Url: https://github.com/boostorg/log/issues/76#issuecomment-461803505  

> Fair enough. Then this should probably be reflected in the documentation.  
  
The docs for `BOOST_LOG_USE_COMPILER_TLS` doesn't say it removes Boost.Thread dependency. In fact, I don't think this is suggested anywhere in the docs. If it is, please feel free to point to the specific wording that gives that impression.  
  
> That being said, are there any plans to use C++11 threading primitives  
  
Not presently. `thread_local` offers worse performance than the lower level keywords like `__thread` or `__declspec(thread)`, which are controlled by `BOOST_LOG_USE_COMPILER_TLS`. It may be better than `thread_specific_ptr`, but given the lower level keywords used for caching that potential difference is not very important.  
  
Other threading primitives, like `std::mutex` or `std::condition_variable`, could be used, but only if they don't affect library ABI (which I'm not sure they would, this needs to be checked). Also, they have different interfaces compared to Boost.Thread. And I know at least some implementations of `std::condition_variable` are buggy with regard to timing functions. Overall, using `std` components is less appealing to me than switching to Boost.Sync instead.

---

## Comment 4

> Username: justinasvd  
> Created at: 2019-02-08 14:17:47 UTC  
> Url: https://github.com/boostorg/log/issues/76#issuecomment-461816665  

Docs don't state that Boost.Thread won't be used, but docs state that   
  
> <..> Generally, these intrinsics allow for a much more efficient access to the storage than any surrogate implementation, be that Boost.Thread or even native operating system API.  
  
That's why I assumed that "oh, if I enable THAT, then a surrogate implementation like Boost.Thread won't be used". Admittedly, it was some sort of wishful thinking.

---

## Comment 5

> Username: Lastique  
> Created at: 2019-02-08 14:33:28 UTC  
> Url: https://github.com/boostorg/log/issues/76#issuecomment-461821465  

I see. I've added a note in c0d0fd3 that should clear that confusion.
