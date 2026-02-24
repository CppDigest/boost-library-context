# #1640 - Syntax error (VS2017 Update 4) [Closed]

> Username: Nekto89  
> Created at: 2019-06-23 19:20:00 UTC  
> Updated at: 2019-09-15 01:39:49 UTC  
> Closed at: 2019-09-15 01:39:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1640  

Boost.Beast can't be used currently on VS2017 Update 4 (minor toolset 14.11). Tried with release Boost 1.70 and on develop branch.  
  
```  
1>------ Build started: Project: lib-beast, Configuration: Debug x64 ------  
1>lib_beast.cpp  
1>C:\dev\boost_beast\beast\include\boost/mp11/detail/mp_with_index.hpp(387): error C2187: syntax error: '{' was unexpected here  
1>C:\dev\boost_beast\beast\include\boost/beast/core/buffer_traits.hpp(123): error C2668: 'boost::asio::buffer_sequence_begin': ambiguous call to overloaded function  
1>C:\dev\boost_beast\beast\include\boost/asio/buffer.hpp(397): note: could be 'const boost::asio::const_buffer *boost::asio::buffer_sequence_begin<add_rvalue_reference<_Ty>::type>(const ConstBuffer &,enable_if<std::is_convertible<const add_rvalue_reference<_Ty>::type*,const boost::asio::const_buffer*>::value,void>::type *) noexcept'  
1>        with  
1>        [  
1>            _Ty=const unknown-type &,  
1>            ConstBuffer=add_rvalue_reference<const unknown-type&>::type  
1>        ]  
1>C:\dev\boost_beast\beast\include\boost/asio/buffer.hpp(387): note: or       'const boost::asio::mutable_buffer *boost::asio::buffer_sequence_begin<add_rvalue_reference<_Ty>::type>(const MutableBuffer &,enable_if<std::is_convertible<const add_rvalue_reference<_Ty>::type*,const boost::asio::mutable_buffer*>::value,void>::type *) noexcept'  
1>        with  
1>        [  
1>            _Ty=const unknown-type &,  
1>            MutableBuffer=add_rvalue_reference<const unknown-type&>::type  
1>        ]  
1>C:\dev\boost_beast\beast\include\boost/beast/core/buffer_traits.hpp(123): note: while trying to match the argument list '(add_rvalue_reference<_Ty>::type)'  
1>        with  
1>        [  
1>            _Ty=const unknown-type &  
1>        ]  
1>C:\dev\boost_beast\beast\include\boost/beast/core/impl/buffers_cat.hpp(128): note: see reference to alias template instantiation 'buffers_iterator_type<unknown-type>' being compiled  
1>C:\dev\boost_beast\beast\include\boost/beast/http/chunk_encode.hpp(251): note: see reference to class template instantiation 'boost::beast::buffers_cat_view<boost::beast::http::detail::chunk_size,boost::asio::const_buffer,boost::beast::http::chunk_crlf>::const_iterator' being compiled  
1>C:\dev\boost_beast\beast\include\boost/beast/core/impl/buffers_cat.hpp(128): error C3546: '...': there are no parameter packs available to expand  
1>C:\dev\boost_beast\beast\include\boost/beast/core/impl/buffers_cat.hpp(128): error C2903: 'variant': symbol is neither a class template nor a function template  
1>Done building project "lib-beast.vcxproj" -- FAILED.  
========== Build: 0 succeeded, 1 failed, 0 up-to-date, 0 skipped ==========  
  
```

---

## Comment 1

> Username: stale[bot]  
> Created at: 2019-07-23 19:27:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1640#issuecomment-514349844  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-07-23 20:08:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1640#issuecomment-514364584  

Is VS2017 Update 4 the latest VS2017?

---

## Comment 3

> Username: Nekto89  
> Created at: 2019-07-27 11:39:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1640#issuecomment-515677701  

No, latest is Update 9.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-07-27 21:29:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1640#issuecomment-515714816  

typically I don't bother fixing errors caused by older versions of Visual Studio, it is too hard to keep up with Microsoft's bugs! If it works in the latest VS2017 (which we do test on appveyor) then the solution is to apply all available service packs and hot fixes to VS2017. If after doing so you still encounter compile errors, feel free to note this on the issue.

---

## Comment 5

> Username: gladiacxtylish  
> Created at: 2019-08-09 00:10:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1640#issuecomment-519730544  

I hit the same issue.

---

## Comment 6

> Username: stale[bot]  
> Created at: 2019-09-08 00:58:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1640#issuecomment-529158999  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 7

> Username: stale[bot]  
> Created at: 2019-09-15 01:39:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1640#issuecomment-531526977  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
