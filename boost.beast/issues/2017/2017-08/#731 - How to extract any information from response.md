# #731 - How to extract any information from response? [Closed]

> Username: xemwebe  
> Created at: 2017-08-11 10:30:02 UTC  
> Updated at: 2017-08-11 16:08:15 UTC  
> Closed at: 2017-08-11 15:30:24 UTC  
> Url: https://github.com/boostorg/beast/issues/731  

After scanning through the documenation and the issue list, I have found no hint how to extract information from the response of, e.g., a GET request.   
  
All examples just send the response to some output stream. Of course, it's possible to output the response into some string and analyse this string to extract all header fields and the body, but I wonder: Is there a better way? Does the library support any method to extract information from the response?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-08-11 12:33:37 UTC  
> Url: https://github.com/boostorg/beast/issues/731#issuecomment-321800831  

Did you see these pages in the doc?  
http://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/using_http/message_containers.html  
http://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__message.html  
http://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__header.html  
http://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__basic_fields.html

---

## Comment 2

> Username: xemwebe  
> Created at: 2017-08-11 15:30:24 UTC  
> Updated at: 2017-08-11 15:31:17 UTC  
> Url: https://github.com/boostorg/beast/issues/731#issuecomment-321844773  

Yes, I have seen this, but this seems to be more focused on how to construct request and responses (which I did need, too, and which worked well).  
  
Still, I do not see how this helps to analyse the response. Currently, I use my own (simple and inefficient) parser for the response message, as is demonstrated in the code based on the sample client you provided.  
```  
//  
// Copyright (c) 2013-2017 Vinnie Falco (vinnie dot falco at gmail dot com)  
//  
// Distributed under the Boost Software License, Version 1.0. (See accompanying  
// file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
//  
  
#include <beast/core.hpp>  
#include <beast/http.hpp>  
#include <beast/version.hpp>  
#include <boost/asio.hpp>  
#include <cstdlib>  
#include <iostream>  
#include <string>  
#include <iostream>  
#include <vector>  
#include <map>  
  
using tcp = boost::asio::ip::tcp; // from <boost/asio.hpp>  
namespace http = beast::http; // from <beast/http.hpp>  
  
                              //! Function that removes whitespaces from the beginning and end of a string  
std::string StripWhiteSpace(const std::string& s)  
{  
  if (s.size() == 0) return s;  
  
  const static char* ws = " \t\v\n\r";  
  size_t start = s.find_first_not_of(ws);  
  size_t end = s.find_last_not_of(ws);  
  if (start>s.size()) return std::string();  
  return s.substr(start, end - start + 1);  
}  
  
typedef std::vector<std::string> StringVec;  
  
//! Split a string into a vector of substrings with \c c as a separator  
/*! White space characters will not be removed and the the strings will not be converted to lowercase letters.  
*/  
StringVec SplitString(const std::string& str, char c)  
{  
  StringVec sv;  
  std::string::size_type ilast = 0;  
  std::string::size_type i = ilast;  
  
  while ((i = str.find(c, i)) < str.size())  
  {  
    sv.push_back(str.substr(ilast, i - ilast));  
    ilast = ++i;  
  }  
  sv.push_back(str.substr(ilast));  
  
  return sv;  
}  
  
//! Split a string into a vector of substrings with \c c as a separator  
/*! White space characters will be removed and the the strings will be converted to lowercase letters.  
*/  
StringVec SplitAndStripString(const std::string& str, char c)  
{  
  StringVec sv;  
  std::string::size_type ilast = 0;  
  std::string::size_type i = ilast;  
  
  while ((i = str.find(c, i)) < str.size())  
  {  
    sv.push_back(StripWhiteSpace(str.substr(ilast, i - ilast)));  
    ilast = ++i;  
  }  
  sv.push_back(str.substr(ilast));  
  
  return sv;  
}  
  
  
std::string ReadResponse(const std::string& input)  
{  
  StringVec sv = SplitAndStripString(input, '\n');  
  
  StringVec FirstLine = SplitAndStripString(sv[0], ' ');  
  size_t i = 1;  
  std::map<std::string, std::string> fields;  
  while (i < sv.size() && sv[i] != "")  
  {  
    StringVec field = SplitAndStripString(sv[i], ':');  
    fields[field[0]] = field[1];  
    ++i;  
  }  
    
  ++i;  
  if(i<sv.size())  
    return sv[i];  
  
  return std::string();  
}  
  
  
int main()  
{  
  // A helper for reporting errors  
  auto const fail =  
    [](std::string what, beast::error_code ec)  
  {  
    std::cerr << what << ": " << ec.message() << std::endl;  
    return EXIT_FAILURE;  
  };  
  
  beast::error_code ec;  
  
  // Set up an asio socket  
  boost::asio::io_service ios;  
  tcp::socket sock{ ios };  
  
  // Make the connection on the IP address we get from a lookup  
  std::string host = "127.0.0.1";  
  unsigned short port = 12345;  
  tcp::endpoint mdendpoint(boost::asio::ip::address(boost::asio::ip::address::from_string(host)), port);  
  sock.connect(mdendpoint, ec); // http://localhost:port  
  if (ec)  
    return fail("connect", ec);  
  
  // Set up an HTTP GET request message  
  http::request<http::string_body> req;  
  req.method(http::verb::get);  
  req.target("/json");  
  req.set(http::field::host, host + ":" +  
    std::to_string(port));  
  req.set(http::field::user_agent, BEAST_VERSION_STRING);  
  req.prepare_payload();  
    
  std::cerr << "prepared request: " << req << std::endl;  
  // Write the HTTP request to the remote host  
  http::write(sock, req, ec);  
  if (ec)  
    return fail("write", ec);  
  
  // This buffer is used for reading and must be persisted  
  beast::flat_buffer b;  
  
  // Declare a container to hold the response  
  http::response<http::dynamic_body> res;  
  
  // Read the response  
  http::read(sock, b, res, ec);  
  if (ec)  
    return fail("read", ec);  
    
  std::ostringstream myos;  
  myos << res << std::endl;  
  std::string body = ReadResponse(myos.str());  
  // Write the message to standard out  
  std::cout << res << std::endl;  
  // Gracefully close the socket  
  sock.shutdown(tcp::socket::shutdown_both, ec);  
  
  // not_connected happens sometimes  
  // so don't bother reporting it.  
  //  
  if (ec && ec != beast::errc::not_connected)  
    return fail("shutdown", ec);  
  
  // If we get here then the connection is closed gracefully  
  return EXIT_SUCCESS;  
}  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-08-11 15:33:53 UTC  
> Updated at: 2017-08-11 15:53:02 UTC  
> Url: https://github.com/boostorg/beast/issues/731#issuecomment-321845569  

>std::map<std::string, std::string> fields;  
  
This makes no sense at all, Beast already provides structured access to the fields. Try this:  
```  
for(auto const& field : res)  
  std::cout << field.name() << " = " << field.value() << "\n";  
  
std::cout << "Server: " << res[http::field::server];  
```  
  
There is no need to parse the serialized response, Beast does that for you!

---

## Comment 4

> Username: xemwebe  
> Created at: 2017-08-11 15:50:47 UTC  
> Url: https://github.com/boostorg/beast/issues/731#issuecomment-321849769  

Well, I hove for something like that.  
  
However, this gives me `'server': is not a member of 'beast::http'`. Is this a newer addition? I am currently using version 82 of beast.  
  
Is it possible to extract the body in a similar way?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-08-11 15:55:40 UTC  
> Updated at: 2017-08-11 15:57:20 UTC  
> Url: https://github.com/boostorg/beast/issues/731#issuecomment-321850900  

Sorry about that, it was a typo. The correct name is `http::field::server`. See:  
http://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__field.html  
  
The container Beast uses for the fields supports `find`, `operator[]`, iteration through `begin` and `end` as shown in the example. See:  
http://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__basic_fields.html  
  
>Is it possible to extract the body in a similar way?  
  
I'm not sure what you mean by "extract" the body, but Beast provides a variety of containers you can use to represent the body. For example you could declare:  
```  
http::response<http::string_body> res;  
```  
  
And then the type of `res.body` will be `std::string`. Then you can access its contents. See:  
http://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/using_http/message_containers.html#beast.using_http.message_containers.body

---

## Comment 6

> Username: xemwebe  
> Created at: 2017-08-11 16:08:15 UTC  
> Url: https://github.com/boostorg/beast/issues/731#issuecomment-321853992  

Great! That's what I needed.   
  
Probably I need to dig a bit deeper into the docs to understand what these different body represenations are used for and how to use them...  
  
Thanks a lot for the quick help (and for providing the library in the first place, of course)!
