# #1727 - Derived class of http::request [Closed]

> Username: zaley2000  
> Created at: 2019-10-09 05:48:04 UTC  
> Updated at: 2019-10-22 07:19:37 UTC  
> Closed at: 2019-10-22 07:19:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1727  

Boost 1.71  
When I derive a class from http::request, like this  
  
class HTTP_EXPORT CHttpRequest  :	public http::request<<http::empty_body>>  
{  
public:  
	CHttpRequest();  
	~CHttpRequest();  
};  
  
compiler of vs2015 givesthese errors  
  
Error C2039 "result": Not a member of "boost::beast::http::message<true,boost::beast::http::empty_body,boost::beast::http::fields>" http D:\ Boost_1_71_0\output\include\boost-1_71\boost\beast\http\impl\message.hpp 343  
Error C2039 "result": Not a member of "boost::beast::http::message<true,boost::beast::http::empty_body,boost::beast::http::fields>" http D:\ Boost_1_71_0\output\include\boost-1_71\boost\beast\http\impl\message.hpp 344  
Error C2039 "result": Not a member of "boost::beast::http::message<true,boost::beast::http::empty_body,boost::beast::http::fields>" http D:\ Boost_1_71_0\output\include\boost-1_71\boost\beast\http\impl\message.hpp 345  
Error C2039 "result": Not a member of "boost::beast::http::message<true,boost::beast::http::empty_body,boost::beast::http::fields>" http D:\ Boost_1_71_0\output\include\boost-1_71\boost\beast\http\impl\message.hpp 393
