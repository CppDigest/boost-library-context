# #2101 - Buffer_Body not working (message const char *	"need buffer"	0x000070000939ad59) [Closed]

> Username: Raj123456788  
> Created at: 2020-10-02 01:26:22 UTC  
> Updated at: 2022-02-08 17:06:07 UTC  
> Closed at: 2022-01-09 05:17:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2101  

When reporting a bug please include the following:  
  
### Version of Beast Release 1.74.0  
  
  
### Steps necessary to reproduce the problem  
  
```  
  
Testservice.cpp:  
//Create a BoostWebserver  
bool TestService::init()  
{  
  
    const char * crt_file = "/Users//Documents/testBoost/server.crt";  
    const char * key_file = "/Users//Documents/testBoost/server.key";  
  
    if (m_server)  
    {  
        BoostWeb::ServiceNode server_node;  
        server_node.host = "127.0.0.1";  
          
        server_node.port = 9000;  
        //server_node.root = "f:/codes_vs2015/munu/munu/www";  
        server_node.timeout = 6000;  
        server_node.body_limit = 0;  
        server_node.protocol = BoostWeb::support_protocol_t::protocol_all;  
        if (!m_web_manager.init(this, &server_node, 1, true, crt_file, key_file, 1))  
        {  
            return (false);  
        }  
          
        return true;  
    }  
  
bool TestService::handle_request(const BoostWeb::HttpConnectionBase & connection, const BoostWeb::HttpRequestBase & request, BoostWeb::HttpResponseBase & response)  
{  
    const char * path = "D:\\test\\main.jpg";  
    std::ifstream file(path, std::ios::binary);  
    file.seekg(0, std::ios::end);  
    std::streamsize size = file.tellg();  
    file.seekg(0, std::ios::beg);  
      
    std::string data(size, 0x0);  
    file.read(&data[0], size);  
      
    response.set_body(std::move(data));  
    return (true);  
}  
//Whenever someone hits http://localhost:9000. I need to respond back with an buffer(main.jpg in this case) that I will be receiving from Testservice and to the client(browser) as buffer. main.jpg is displayed in the browser. The content is dynamic(buffer) can be any image that testservice will be receiving  
In HttpUtility.h  
    boost::beast::http::response<boost::beast::http::string_body, Fields> res;  
       HttpRequest<Body, Fields> request(req);  
            HttpResponse<boost::beast::http::string_body, Fields> response(res);  
            service->handle_request(connection, request, response); // call Testservice to get the data. Response is string as mentioned above in handle_request method.  
  auto f= response.get_body(); // f is the string data  
   char char_array[200];  
              
              // copying the contents of the  
              // string to char array  
   strcpy(char_array, f.c_str());  
 http::response<http::buffer_body> res{http::status::ok, req.version()}; // Is this right way to define the buffer_body?  
 res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
 res.set(http::field::content_type, "image/png");  
 res.keep_alive(req.keep_alive());  
 res.body().data = char_array; // Not working  
 res.body().size = 200; //Not working  
 res.prepare_payload();  
 return send(std::move(res));  
  
```  
**Output: str content: https::send error (5) {need buffer}**  
### All relevant compiler information  
  
Please let me know if I have missed anything.

---

## Comment 1

> Username: Raj123456788  
> Created at: 2020-10-02 17:39:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2101#issuecomment-702867235  

@madmongo1 : Can you please reply?

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-10-02 18:19:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2101#issuecomment-702886264  

Hi there. Would you mind presenting a minimal complete example that exhibits the issue?  
  
I don’t have the contextual information around the above snippet. A compete short program would help me to answer your question more effectively.

---

## Comment 3

> Username: Raj123456788  
> Created at: 2020-10-02 18:45:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2101#issuecomment-702897985  

Hi @madmongo1 : Edited the question. please try again. Let me know if  anything I missed?

---

## Comment 4

> Username: madmongo1  
> Created at: 2020-10-02 18:51:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2101#issuecomment-702900777  

I need a short program I can just copy/paste into my editor and compile. No need for ssl stuff, just the response setup, connection and async/sync write so I can trap the error.

---

## Comment 5

> Username: Raj123456788  
> Created at: 2020-10-02 18:56:26 UTC  
> Updated at: 2020-10-02 18:58:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2101#issuecomment-702903189  

```  
  
#ifndef BOOST_WEB_HTTP_UTILITY_H  
#define BOOST_WEB_HTTP_UTILITY_H  
  
  
#include <string>  
#include <algorithm>  
#include <boost/beast/http.hpp>  
#include "boost_web.h"  
#include "http_request.hpp"  
#include "http_response.hpp"  
#include "http_message.hpp"  
#include <iostream>  
#include <fstream>  
  
  
namespace BoostWeb { // namespace BoostWeb begin  
  
extern const std::string & get_mime_type(const std::string & filename);  
extern std::string path_catenate(const std::string & base, const std::string & path);  
  
template <class Body, class Fields, class Send>  
void handle_request(WebServiceBase * service, const std::string & doc_root, const HttpConnectionBase & connection, boost::beast::http::request<Body, Fields> && req, Send && send)  
  
{  
  
    auto const bad_request = [&req](boost::beast::string_view why)  
    {  
        boost::beast::http::response<boost::beast::http::string_body> res{ boost::beast::http::status::bad_request, req.version() };  
        res.set(boost::beast::http::field::server, "boost web server 1.0 by yanrk");  
        res.set(boost::beast::http::field::content_type, "text/html");  
        res.set(boost::beast::http::field::access_control_allow_origin, "*");  
        res.keep_alive(req.keep_alive());  
        res.body() = std::string(why);  
        res.prepare_payload();  
        return (res);  
    };  
  
    auto const not_found = [&req](boost::beast::string_view target)  
    {  
        boost::beast::http::response<boost::beast::http::string_body> res{ boost::beast::http::status::not_found, req.version() };  
        res.set(boost::beast::http::field::server, "boost web server 1.0 by yanrk");  
        res.set(boost::beast::http::field::content_type, "text/html");  
        res.set(boost::beast::http::field::access_control_allow_origin, "*");  
        res.keep_alive(req.keep_alive());  
        res.body() = "The resource '" + std::string(target) + "' was not found";  
        res.prepare_payload();  
        return (res);  
        
    };  
  
      
    auto const background = [&req](boost::beast::string_view target)  
    {  
        boost::beast::http::response<boost::beast::http::string_body> res{ };  
                res.set(boost::beast::http::field::content_type, "image/jpeg; binary");  
                res.content_length(200);  
  
                std::vector<char> buffer(size);  
                if (file.read(buffer.data(), size))  
                {  
                    res.body()=buffer.data();  
                    res.prepare_payload();  
                      
                }  
                  
                  std::cout << "Background" <<  std::endl;  
          
                return(res);  
    };  
      
    auto const server_error = [&req](boost::beast::string_view what)  
    {  
        boost::beast::http::response<boost::beast::http::string_body> res{ boost::beast::http::status::internal_server_error, req.version() };  
        res.set(boost::beast::http::field::server, "boost web server 1.0 by yanrk");  
        res.set(boost::beast::http::field::content_type, "text/html");  
        res.set(boost::beast::http::field::access_control_allow_origin, "*");  
        res.keep_alive(req.keep_alive());  
        res.body() = "An error occurred: '" + std::string(what) + "'";  
        res.prepare_payload();  
        return (res);  
    };  
      
      
    const std::string request_target(req.target());  
    if(request_target=="/mask")  
    {  
        std::cout << "Mask" <<  std::endl;  
    };  
    if(request_target=="/background")  
    {  
       // service->handle_request(connection, request, response);  
        //return (send(background(req.target())));  
    }  
  
    std::string request_target_append_slash(request_target + "/");  
    std::replace(request_target_append_slash.begin(), request_target_append_slash.end(), '\\', '/');  
    if (request_target.empty() || '/' != request_target.front() || std::string::npos != request_target_append_slash.find("/../"))  
    {  
        return (send(bad_request("Illegal request-target '" + request_target + "'")));  
    }  
  
    if (service->target_is_path(request_target))  
    {  
        std::string path = path_catenate(doc_root, request_target);  
        if ('/' == path.back())  
        {  
            path.append("index.html");  
        }  
  
        boost::beast::error_code ec;  
        boost::beast::http::file_body::value_type body;  
        body.open(path.c_str(), boost::beast::file_mode::scan, ec);  
  
        if (boost::system::errc::no_such_file_or_directory == ec)  
        {  
            return (send(not_found(req.target())));  
        }  
  
        if (ec)  
        {  
            return (send(server_error(ec.message())));  
        }  
  
        /*  
         * cache the body size since we need it after the move operate  
         */  
        const uint64_t body_size = body.size();  
  
        if (boost::beast::http::verb::head == req.method())  
        {  
            boost::beast::http::response<boost::beast::http::empty_body> res{ boost::beast::http::status::ok, req.version() };  
            res.set(boost::beast::http::field::server, "boost web server 1.0 by yanrk");  
            res.set(boost::beast::http::field::content_type, get_mime_type(path));  
            res.set(boost::beast::http::field::access_control_allow_origin, "*");  
            res.content_length(body_size);  
            res.keep_alive(req.keep_alive());  
            return (send(std::move(res)));  
        }  
        else if (boost::beast::http::verb::get == req.method())  
        {  
            boost::beast::http::response<boost::beast::http::file_body> res{ std::piecewise_construct, std::make_tuple(std::move(body)), std::make_tuple(boost::beast::http::status::ok, req.version()) };  
            res.set(boost::beast::http::field::server, "boost web server 1.0 by yanrk");  
            res.set(boost::beast::http::field::content_type, get_mime_type(path));  
            res.set(boost::beast::http::field::access_control_allow_origin, "*");  
            res.content_length(body_size);  
            res.keep_alive(req.keep_alive());  
            return (send(std::move(res)));  
        }  
        else  
        {  
            return (send(bad_request("Unsupported HTTP-method '" + std::string(req.method_string()) + "' while request file")));  
        }  
    }  
    else  
    {  
  
              
              
              
              
              
              
            if (boost::beast::http::verb::connect < req.method())  
            {  
                return (send(bad_request("Unsupported HTTP-method '" + std::string(req.method_string()) + "'")));  
            }  
  
            boost::beast::http::response<boost::beast::http::string_body, Fields> res;  
            HttpRequest<Body, Fields> request(req);  
            HttpResponse<boost::beast::http::string_body, Fields> response(res);  
            service->handle_request(connection, request, response);  
  
            auto f= response.get_body();  
 std::make_tuple(boost::beast::http::status::ok, req.version()) };  
            boost::beast::http::response<boost::beast::http::buffer_body> res1{ boost::beast::http::status::ok, req.version() };  
              //int n = f.length();  
              
              // declaring character array  
              char char_array[200];  
              
              // copying the contents of the  
              // string to char array  
            strcpy(char_array, f.c_str());  
            res1.body().data= char_array;  
            //res1.body().data= response.get_body();  
            res1.body().size=1200;  
            res1.set(boost::beast::http::field::content_type, "image/jpeg; binary");  
            //res.content_length(200);  
            res1.keep_alive(req.keep_alive());  
            return (send(std::move(res1)));  
                     
  
        }  
  
    }  
}  
  
} // namespace BoostWeb end  
  
  
#endif // BOOST_WEB_HTTP_UTILITY_H  
  
```  
Hope this is good enough?

---

## Comment 6

> Username: Raj123456788  
> Created at: 2020-10-03 15:22:25 UTC  
> Updated at: 2020-10-05 16:20:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2101#issuecomment-703119947  

Sorry to bother you again  @madmongo1 @vinniefalco  did you find anything that needs to be changed.

---

## Comment 7

> Username: Raj123456788  
> Created at: 2020-10-05 16:08:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2101#issuecomment-703731459  

<img width="653" alt="Screen Shot 2020-10-05 at 9 07 56 AM" src="https://user-images.githubusercontent.com/16295521/95104109-51d59d80-06ea-11eb-8802-10e7f0069dcc.png">  
  
I can see the data but why does it say needs buffer error?

---

## Comment 8

> Username: stale[bot]  
> Created at: 2020-11-16 03:45:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2101#issuecomment-727717658  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 9

> Username: Raj123456788  
> Created at: 2020-11-17 20:07:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2101#issuecomment-729171768  

No, it is not resolved. Still cannot get to the bottom of this. Will keep you posted.

---

## Comment 10

> Username: stale[bot]  
> Created at: 2020-12-25 12:12:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2101#issuecomment-751240893  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 11

> Username: Raj123456788  
> Created at: 2020-12-28 19:58:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2101#issuecomment-751846203  

Sorry for a late reply. No it is not been resolved. you can close this. I will reopen if I find more info.

---

## Comment 12

> Username: madmongo1  
> Created at: 2020-12-28 23:34:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2101#issuecomment-751896217  

The file above is not a complete program. It would really help me if you could create the simplest possible complete program which demonstrates the issue.  
(often performing this exercise actually reveals the problem).

---

## Comment 13

> Username: stale[bot]  
> Created at: 2021-05-29 16:11:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2101#issuecomment-850857916  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 14

> Username: stale[bot]  
> Created at: 2022-01-09 05:16:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2101#issuecomment-1008232347  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---

## Comment 15

> Username: Tarrowren  
> Created at: 2022-02-08 13:01:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2101#issuecomment-1032583395  

when downloading a 140MB file, throw error "need buffer"    
  
code:   
```cpp  
void download(const char *file_url, const char *file_path)  
  {  
    size_t len;  
    char buf[8192];  
  
    asio::io_context ioc;  
    asio::ip::tcp::resolver resolver(ioc);  
    asio::ip::tcp::socket stream{ioc};  
  
    asio::connect(stream, resolver.resolve(host, port));  
    beast::http::request<beast::http::empty_body> req{beast::http::verb::get, file_url, version};  
    beast::http::write(stream, req);  
  
    beast::flat_buffer buffer;  
    beast::http::parser<false, beast::http::buffer_body> parser;  
  
    parser.body_limit((numeric_limits<uint64_t>::max)());  
  
    beast::http::read_header(stream, buffer, parser);  
  
    const auto code = parser.get().result_int();  
  
    if (code != 200)  
    {  
      ostringstream s(ios::out | ios::binary);  
      s << "HTTP code: " << code << ", message: ";  
  
      while (!parser.is_done())  
      {  
        parser.get().body().data = buf;  
        parser.get().body().size = sizeof(buf);  
        len = beast::http::read(stream, buffer, parser);  
        s.write(buf, len);  
      }  
  
      throw std::runtime_error(s.str());  
    }  
    else  
    {  
      ofstream f(file_path, ios::out | ios::binary);  
  
      while (!parser.is_done())  
      {  
        parser.get().body().data = buf;  
        parser.get().body().size = sizeof(buf);  
        len = beast::http::read(stream, buffer, parser);  
        f.write(buf, len);  
      }  
  
      f.close();  
    }  
  }  
```

---

## Comment 16

> Username: madmongo1  
> Created at: 2022-02-08 17:06:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2101#issuecomment-1032851460  

From memory, the flat_buffer's `max_size` defaults to 1MB. You may need to increase this if you wish to download the entire file into the buffer.  
  
https://www.boost.org/doc/libs/1_78_0/libs/beast/doc/html/beast/ref/boost__beast__basic_flat_buffer/max_size.html
