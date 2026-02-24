# #179 - boost::asio::async_write compile error on Visual Studio 2012 with Boost 1.69.0 [Closed]

> Username: RockinRoel  
> Created at: 2018-12-19 15:17:20 UTC  
> Updated at: 2020-12-30 00:58:07 UTC  
> Closed at: 2020-12-30 00:58:06 UTC  
> Url: https://github.com/boostorg/asio/issues/179  

I updated our binary build toolchain from Boost 1.68.0 to Boost 1.69.0, and now, only with Visual Studio 2012 (not 2010, not any version after 2012, or any other compiler), this code fails to compile:  
  
```cpp  
#include <boost/asio.hpp>  
#include <boost/function.hpp>  
#include <boost/system/system_error.hpp>  
  
int main()  
{  
  boost::asio::io_context ctx;  
  boost::asio::ip::tcp::socket socket(ctx);  
  boost::asio::streambuf buffer;  
  boost::function<void(boost::system::error_code const&, std::size_t const&)> handler;  
  
  boost::asio::async_write(socket, buffer, handler);  
}  
```  
  
This is of course a little test I wrote up quickly, not a working example that makes sense, but it demonstrates what we're trying to do.  
  
This is the output I get:  
```  
1>------ Build started: Project: TestAsioCompile, Configuration: Debug Win32 ------  
1>  TestAsioCompile.cpp  
1>c:\users\roel\boost_1_69_0\boost\asio\impl\write.hpp(488): error C2784: '::boost::asio::async_result<::boost::asio::decay<_Vx0_t>::type,void(boost::system::error_code,size_t)>::return_type boost::asio::async_write(AsyncWriteStream &,boost::asio::basic_streambuf<Allocator> &,CompletionCondition,WriteHandler &&)' : could not deduce template argument for 'boost::asio::basic_streambuf<Allocator> &' from 'boost::asio::const_buffers_1'  
1>          c:\users\roel\boost_1_69_0\boost\asio\write.hpp(914) : see declaration of 'boost::asio::async_write'  
1>          c:\users\roel\boost_1_69_0\boost\asio\impl\write.hpp(483) : while compiling class template member function 'void boost::asio::detail::write_dynbuf_op<AsyncWriteStream,DynamicBuffer,CompletionCondition,WriteHandler>::operator ()(const boost::system::error_code &,size_t,int)'  
1>          with  
1>          [  
1>              AsyncWriteStream=boost::asio::ip::tcp::socket,  
1>              DynamicBuffer=boost::asio::basic_streambuf_ref<std::allocator<char>>,  
1>              CompletionCondition=boost::asio::detail::transfer_all_t,  
1>              WriteHandler=boost::function<void (const boost::system::error_code &,const size_t &)>  
1>          ]  
1>          c:\users\roel\boost_1_69_0\boost\asio\impl\write.hpp(632) : see reference to function template instantiation 'void boost::asio::detail::write_dynbuf_op<AsyncWriteStream,DynamicBuffer,CompletionCondition,WriteHandler>::operator ()(const boost::system::error_code &,size_t,int)' being compiled  
1>          with  
1>          [  
1>              AsyncWriteStream=boost::asio::ip::tcp::socket,  
1>              DynamicBuffer=boost::asio::basic_streambuf_ref<std::allocator<char>>,  
1>              CompletionCondition=boost::asio::detail::transfer_all_t,  
1>              WriteHandler=boost::function<void (const boost::system::error_code &,const size_t &)>  
1>          ]  
1>          c:\users\roel\boost_1_69_0\boost\asio\impl\write.hpp(632) : see reference to class template instantiation 'boost::asio::detail::write_dynbuf_op<AsyncWriteStream,DynamicBuffer,CompletionCondition,WriteHandler>' being compiled  
1>          with  
1>          [  
1>              AsyncWriteStream=boost::asio::ip::tcp::socket,  
1>              DynamicBuffer=boost::asio::basic_streambuf_ref<std::allocator<char>>,  
1>              CompletionCondition=boost::asio::detail::transfer_all_t,  
1>              WriteHandler=boost::function<void (const boost::system::error_code &,const size_t &)>  
1>          ]  
1>          c:\users\roel\boost_1_69_0\boost\asio\impl\write.hpp(608) : see reference to function template instantiation 'void boost::asio::async_write<AsyncWriteStream,DynamicBuffer,boost::asio::detail::transfer_all_t,boost::function<Signature>&>(AsyncWriteStream &,DynamicBuffer &&,CompletionCondition,WriteHandler,void *)' being compiled  
1>          with  
1>          [  
1>              AsyncWriteStream=boost::asio::ip::tcp::socket,  
1>              DynamicBuffer=boost::asio::basic_streambuf_ref<std::allocator<char>>,  
1>              Signature=void (const boost::system::error_code &,const size_t &),  
1>              CompletionCondition=boost::asio::detail::transfer_all_t,  
1>              WriteHandler=boost::function<void (const boost::system::error_code &,const size_t &)> &  
1>          ]  
1>          c:\users\roel\boost_1_69_0\boost\asio\impl\write.hpp(652) : see reference to function template instantiation 'void boost::asio::async_write<AsyncWriteStream,boost::asio::basic_streambuf_ref<Allocator>,boost::function<Signature>&>(AsyncWriteStream &,DynamicBuffer &&,WriteHandler,void *)' being compiled  
1>          with  
1>          [  
1>              AsyncWriteStream=boost::asio::ip::tcp::socket,  
1>              Allocator=std::allocator<char>,  
1>              Signature=void (const boost::system::error_code &,const size_t &),  
1>              DynamicBuffer=boost::asio::basic_streambuf_ref<std::allocator<char>>,  
1>              WriteHandler=boost::function<void (const boost::system::error_code &,const size_t &)> &  
1>          ]  
1>          c:\users\roel\documents\visual studio 2012\projects\testasiocompile\testasiocompile\testasiocompile.cpp(17) : see reference to function template instantiation 'void boost::asio::async_write<boost::asio::ip::tcp::socket,std::allocator<_Ty>,boost::function<Signature>&>(AsyncWriteStream &,boost::asio::basic_streambuf<> &,WriteHandler)' being compiled  
1>          with  
1>          [  
1>              _Ty=char,  
1>              Signature=void (const boost::system::error_code &,const size_t &),  
1>              AsyncWriteStream=boost::asio::ip::tcp::socket,  
1>              WriteHandler=boost::function<void (const boost::system::error_code &,const size_t &)> &  
1>          ]  
1>          c:\users\roel\boost_1_69_0\boost\asio\write.hpp(914) : see declaration of 'boost::asio::async_write'  
1>c:\users\roel\boost_1_69_0\boost\asio\impl\write.hpp(488): error C2780: '::boost::asio::async_result<::boost::asio::decay<_Fun>::type,void(boost::system::error_code,size_t)>::return_type boost::asio::async_write(AsyncWriteStream &,boost::asio::basic_streambuf<Allocator> &,WriteHandler &&)' : expects 3 arguments - 4 provided  
1>          c:\users\roel\boost_1_69_0\boost\asio\write.hpp(852) : see declaration of 'boost::asio::async_write'  
1>c:\users\roel\boost_1_69_0\boost\asio\impl\write.hpp(488): error C2893: Failed to specialize function template '::boost::asio::async_result<::boost::asio::decay<_Vx0_t>::type,void(boost::system::error_code,size_t)>::return_type boost::asio::async_write(AsyncWriteStream &,DynamicBuffer &&,CompletionCondition,WriteHandler &&,enable_if<boost::asio::is_dynamic_buffer<decay<CompletionToken>::type>::value>::type *)'  
1>          With the following template arguments:  
1>          'boost::asio::ip::tcp::socket'  
1>          'boost::asio::const_buffers_1'  
1>          'boost::asio::detail::transfer_all_t'  
1>          'boost::asio::detail::write_dynbuf_op<AsyncWriteStream,DynamicBuffer,CompletionCondition,WriteHandler>'  
1>          with  
1>          [  
1>              AsyncWriteStream=boost::asio::ip::tcp::socket,  
1>              DynamicBuffer=boost::asio::basic_streambuf_ref<std::allocator<char>>,  
1>              CompletionCondition=boost::asio::detail::transfer_all_t,  
1>              WriteHandler=boost::function<void (const boost::system::error_code &,const size_t &)>  
1>          ]  
1>c:\users\roel\boost_1_69_0\boost\asio\impl\write.hpp(488): error C2893: Failed to specialize function template '::boost::asio::async_result<::boost::asio::decay<_Fun>::type,void(boost::system::error_code,size_t)>::return_type boost::asio::async_write(AsyncWriteStream &,DynamicBuffer &&,WriteHandler &&,enable_if<boost::asio::is_dynamic_buffer<decay<CompletionToken>::type>::value>::type *)'  
1>          With the following template arguments:  
1>          'boost::asio::ip::tcp::socket'  
1>          'boost::asio::const_buffers_1'  
1>          'boost::asio::detail::transfer_all_t &'  
1>c:\users\roel\boost_1_69_0\boost\asio\impl\write.hpp(488): error C2893: Failed to specialize function template '::boost::asio::async_result<::boost::asio::decay<_Vx0_t>::type,void(boost::system::error_code,size_t)>::return_type boost::asio::async_write(AsyncWriteStream &,const ConstBufferSequence &,CompletionCondition,WriteHandler &&,enable_if<boost::asio::is_const_buffer_sequence<ConstBufferSequence>::value>::type *)'  
1>          With the following template arguments:  
1>          'boost::asio::ip::tcp::socket'  
1>          'boost::asio::const_buffers_1'  
1>          'boost::asio::detail::transfer_all_t'  
1>          'boost::asio::detail::write_dynbuf_op<AsyncWriteStream,DynamicBuffer,CompletionCondition,WriteHandler>'  
1>          with  
1>          [  
1>              AsyncWriteStream=boost::asio::ip::tcp::socket,  
1>              DynamicBuffer=boost::asio::basic_streambuf_ref<std::allocator<char>>,  
1>              CompletionCondition=boost::asio::detail::transfer_all_t,  
1>              WriteHandler=boost::function<void (const boost::system::error_code &,const size_t &)>  
1>          ]  
1>c:\users\roel\boost_1_69_0\boost\asio\impl\write.hpp(488): error C2893: Failed to specialize function template '::boost::asio::async_result<::boost::asio::decay<_Fun>::type,void(boost::system::error_code,size_t)>::return_type boost::asio::async_write(AsyncWriteStream &,const ConstBufferSequence &,WriteHandler &&,enable_if<boost::asio::is_const_buffer_sequence<ConstBufferSequence>::value>::type *)'  
1>          With the following template arguments:  
1>          'boost::asio::ip::tcp::socket'  
1>          'boost::asio::const_buffers_1'  
1>          'boost::asio::detail::transfer_all_t &'  
1>  stdafx.cpp  
1>  Generating Code...  
========== Build: 0 succeeded, 1 failed, 0 up-to-date, 0 skipped ==========  
```

---

## Comment 1

> Username: imag0r  
> Created at: 2019-02-21 15:51:28 UTC  
> Url: https://github.com/boostorg/asio/issues/179#issuecomment-466051556  

Anyone, any workarounds?

---

## Comment 2

> Username: ghost  
> Created at: 2020-12-30 00:58:05 UTC  
> Url: https://github.com/boostorg/asio/issues/179#issuecomment-752290815  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#659](https://github.com/chriskohlhoff/asio/issues/659).
