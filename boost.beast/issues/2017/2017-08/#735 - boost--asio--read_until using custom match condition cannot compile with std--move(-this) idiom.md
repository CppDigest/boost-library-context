# #735 - boost::asio::read_until using custom match condition cannot compile with std::move(*this) idiom [Closed]

> Username: aschuess  
> Created at: 2017-08-13 18:09:24 UTC  
> Updated at: 2017-09-01 03:32:05 UTC  
> Closed at: 2017-09-01 03:32:05 UTC  
> Url: https://github.com/boostorg/beast/issues/735  

Compiler VS2017  
#define BOOST_BEAST_VERSION 104  
  
I am attempting to implement a custom match condition in combination with boost::asio::async_read_until as part of a beast composed operation.  The syntax I'm using for the match condition is as follows:  
  
```  
class match_char  
{  
public:  
  explicit match_char(char c) : c_(c) {}  
  
  template <typename Iterator>  
  std::pair<Iterator, bool> operator()(  
      Iterator begin, Iterator end) const  
  {  
    Iterator i = begin;  
    while (i != end)  
      if (c_ == *i++)  
        return std::make_pair(i, true);  
    return std::make_pair(i, false);  
  }  
  
private:  
  char c_;  
};  
  
namespace asio {  
  template <> struct is_match_condition<match_char>  
    : public boost::true_type {};  
} // namespace asio  
...  
...  
  
template<class AsyncStream, class DynamicBuffer, class Handler>  
void read_msg_op<AsyncStream, DynamicBuffer, Handler>::  
operator()(boost::beast::error_code ec, std::size_t bytes_transferred)  
{  
//...  
return boost::asio::async_read_until(s, b, match_char('a'), std::move(*this));  
}  
  
```  
  
And the compiler cannot figure figure out which overload to use.  Is there some way to help it figure it out?  I tried casting the std::move(*this) but cannot seem to figure out the magic code.  When using a straight `void handler(error_code ec, std::size) {}` for the handler argument it compiles fine.  Below is the error output from VS2017.  Many thanks :)  
  
```  
1>C:\Users\andrew\builds\dev\src\comm/read_msg.hpp(310): error C2665: 'boost::asio::read_until': none of the 5 overloads could convert all the argument types  
1>C:\Users\andrew\builds\boost_1_64_0\boost/asio/impl/read_until.hpp(265): note: could be 'size_t boost::asio::read_until<AsyncStream,std::allocator<char>,comm::detail::match_char>(SyncReadStream &,boost::asio::basic_streambuf<std::allocator<char>> &,MatchCondition,boost::system::error_code &,void *)'  
1>        with  
1>        [  
1>            AsyncStream=boost::beast::test::string_iostream,  
1>            SyncReadStream=boost::beast::test::string_iostream,  
1>            MatchCondition=comm::detail::match_char  
1>        ]  
1>C:\Users\andrew\builds\boost_1_64_0\boost/asio/impl/read_until.hpp(317): note: or       'size_t boost::asio::read_until<AsyncStream,std::allocator<char>,comm::detail::match_char>(SyncReadStream &,boost::asio::basic_streambuf<std::allocator<char>> &,MatchCondition,void *)'  
1>        with  
1>        [  
1>            AsyncStream=boost::beast::test::string_iostream,  
1>            SyncReadStream=boost::beast::test::string_iostream,  
1>            MatchCondition=comm::detail::match_char  
1>        ]  
1>C:\Users\andrew\builds\boost_1_64_0\boost/asio/impl/read_until.hpp(206): note: or       'size_t boost::asio::read_until<AsyncStream,std::allocator<char>>(SyncReadStream &,boost::asio::basic_streambuf<std::allocator<char>> &,const boost::regex &,boost::system::error_code &)'  
1>        with  
1>        [  
1>            AsyncStream=boost::beast::test::string_iostream,  
1>            SyncReadStream=boost::beast::test::string_iostream  
1>        ]  
1>C:\Users\andrew\builds\boost_1_64_0\boost/asio/impl/read_until.hpp(139): note: or       'size_t boost::asio::read_until<AsyncStream,std::allocator<char>>(SyncReadStream &,boost::asio::basic_streambuf<std::allocator<char>> &,const std::string &,boost::system::error_code &)'  
1>        with  
1>        [  
1>            AsyncStream=boost::beast::test::string_iostream,  
1>            SyncReadStream=boost::beast::test::string_iostream  
1>        ]  
1>C:\Users\andrew\builds\boost_1_64_0\boost/asio/impl/read_until.hpp(48): note: or       'size_t boost::asio::read_until<AsyncStream,std::allocator<char>>(SyncReadStream &,boost::asio::basic_streambuf<std::allocator<char>> &,char,boost::system::error_code &)'  
1>        with  
1>        [  
1>            AsyncStream=boost::beast::test::string_iostream,  
1>            SyncReadStream=boost::beast::test::string_iostream  
1>        ]  
1>C:\Users\andrew\builds\dev\src\comm/read_msg.hpp(310): note: while trying to match the argument list '(boost::beast::test::string_iostream, boost::asio::streambuf, comm::detail::match_char, comm::read_msg_op<AsyncStream,DynamicBuffer,____C_A_T_C_H____T_E_S_T____74::<lambda_881e0622dc11cafb22751f624439b95e>>)'  
1>        with  
1>        [  
1>            AsyncStream=boost::beast::test::string_iostream,  
1>            DynamicBuffer=comm::wire_msg  
1>        ]  
1>C:\Users\andrew\builds\dev\src\comm/read_msg.hpp(286): note: while compiling class template member function 'void comm::read_msg_op<AsyncStream,DynamicBuffer,____C_A_T_C_H____T_E_S_T____74::<lambda_881e0622dc11cafb22751f624439b95e>>::operator ()(boost::beast::error_code,::size_t)'  
1>        with  
1>        [  
1>            AsyncStream=boost::beast::test::string_iostream,  
1>            DynamicBuffer=comm::wire_msg  
1>        ]  
1>C:\Users\andrew\builds\dev\src\comm/read_msg.hpp(483): note: see reference to function template instantiation 'void comm::read_msg_op<AsyncStream,DynamicBuffer,____C_A_T_C_H____T_E_S_T____74::<lambda_881e0622dc11cafb22751f624439b95e>>::operator ()(boost::beast::error_code,::size_t)' being compiled  
1>        with  
1>        [  
1>            AsyncStream=boost::beast::test::string_iostream,  
1>            DynamicBuffer=comm::wire_msg  
1>        ]  
1>C:\Users\andrew\builds\dev\src\comm/read_msg.hpp(483): note: see reference to class template instantiation 'comm::read_msg_op<AsyncStream,DynamicBuffer,____C_A_T_C_H____T_E_S_T____74::<lambda_881e0622dc11cafb22751f624439b95e>>' being compiled  
1>        with  
1>        [  
1>            AsyncStream=boost::beast::test::string_iostream,  
1>            DynamicBuffer=comm::wire_msg  
1>        ]  
1>C:\Users\andrew\builds\dev\src\comm\test\read_msg_test.cpp(55): note: see reference to function template instantiation 'void comm::read_msg<boost::beast::test::string_iostream,comm::wire_msg,____C_A_T_C_H____T_E_S_T____74::<lambda_881e0622dc11cafb22751f624439b95e>&>(AsyncStream &,DynamicBuffer &,CompletionToken)' being compiled  
1>        with  
1>        [  
1>            AsyncStream=boost::beast::test::string_iostream,  
1>            DynamicBuffer=comm::wire_msg,  
1>            CompletionToken=____C_A_T_C_H____T_E_S_T____74::<lambda_881e0622dc11cafb22751f624439b95e> &  
1>        ]  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-08-13 18:45:47 UTC  
> Url: https://github.com/boostorg/beast/issues/735#issuecomment-322059615  

Is `read_msg_op` your class? I don't see the declaration. If you can provide a repository that I can clone and try to build in Visual Studio, I could give it a shot.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-09-01 03:32:05 UTC  
> Url: https://github.com/boostorg/beast/issues/735#issuecomment-326478555  

Is this issue still relevant? I haven't heard anything more about it
