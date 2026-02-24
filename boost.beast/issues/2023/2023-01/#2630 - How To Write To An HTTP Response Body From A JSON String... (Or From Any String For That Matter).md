# #2630 - How To Write To An HTTP Response Body From A JSON String... (Or From Any String For That Matter) [Closed]

> Username: nuertey  
> Created at: 2023-01-25 18:35:27 UTC  
> Updated at: 2023-02-16 02:46:41 UTC  
> Closed at: 2023-02-16 02:46:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2630  

Hello @vinniefalco and all,  
Thanks for great libraries. I am putting together a segment of code using two of your great libraries, Boost.Beast and Boost.JSON and I seem to be having issues assigning a JSON string to the HTTP Response body.   
  
I am on Boost 1.81.0, Ubuntu 16.04, g++ (GCC) 11.2.0    
  
First the error message. Note that I simply replaced my actual problematic line of code with a simple std::string() to see if that would compile and that failed as well:  
  
```  
[ 50%] Built target fmt  
Consolidate compiler generated dependencies of target ...HTTPServer  
[ 66%] Building CXX object CMakeFiles/...HTTPServer.dir/HTTPServer.cpp.o  
In file included from /...HTTPServer/HTTPServer.h:30,  
                 from /...HTTPServer/HTTPServer.cpp:1:  
/...HTTPServer/Protocol.h: In instantiation of ‘boost::beast::http::message_generator handle_request(boost::beast::http::request<Body, boost::beast::http::basic_fields<Allocator> >&&) [with Body = boost::beast::http::basic_string_body<char>; Allocator = std::allocator<char>; boost::beast::http::request<Body, boost::beast::http::basic_fields<Allocator> > = boost::beast::http::message<true, boost::beast::http::basic_string_body<char>, boost::beast::http::basic_fields<std::allocator<char> > >]’:  
/...HTTPServer/HTTPServer.h:134:37:   required from here  
/...HTTPServer/Protocol.h:522:32: error: no match for ‘operator=’ (operand types are ‘boost::beast::http::empty_body::value_type’ and ‘std::string’ {aka ‘std::__cxx11::basic_string<char>’})  
  522 |                     res.body() = std::string("Nuertey");  
      |                     ~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~  
In file included from /usr/local/boost_1_81_0/boost/beast/http.hpp:21,  
                 from /...HTTPServer/Protocol.h:30,  
                 from /...HTTPServer/HTTPServer.h:30,  
                 from ...HTTPServer/HTTPServer.cpp:1:  
/usr/local/boost_1_81_0/boost/beast/http/empty_body.hpp:38:12: note: candidate: ‘constexpr boost::beast::http::empty_body::value_type& boost::beast::http::empty_body::value_type::operator=(const boost::beast::http::empty_body::value_type&)’  
   38 |     struct value_type  
      |            ^~~~~~~~~~  
/usr/local/boost_1_81_0/boost/beast/http/empty_body.hpp:38:12: note:   no known conversion for argument 1 from ‘std::string’ {aka ‘std::__cxx11::basic_string<char>’} to ‘const boost::beast::http::empty_body::value_type&’  
/usr/local/boost_1_81_0/boost/beast/http/empty_body.hpp:38:12: note: candidate: ‘constexpr boost::beast::http::empty_body::value_type& boost::beast::http::empty_body::value_type::operator=(boost::beast::http::empty_body::value_type&&)’  
/usr/local/boost_1_81_0/boost/beast/http/empty_body.hpp:38:12: note:   no known conversion for argument 1 from ‘std::string’ {aka ‘std::__cxx11::basic_string<char>’} to ‘boost::beast::http::empty_body::value_type&&’  
CMakeFiles/...HTTPServer.dir/build.make:75: recipe for target 'CMakeFiles/...HTTPServer.dir/HTTPServer.cpp.o' failed  
make[2]: *** [CMakeFiles/...HTTPServer.dir/HTTPServer.cpp.o] Error 1  
CMakeFiles/Makefile2:99: recipe for target 'CMakeFiles/...HTTPServer.dir/all' failed  
make[1]: *** [CMakeFiles/...HTTPServer.dir/all] Error 2  
Makefile:135: recipe for target 'all' failed  
make: *** [all] Error 2  
  
```  
  
And then here are the relevant pieces of my code, a lot cribbed from your own notes:  
  
```  
    if (req.method() == http::verb::post)  
    {     
        // "Parsing  
        //  
        // JSON can be parsed into the value container in one step using  
        // a free function. In the following snippet, a parse error is   
        // indicated by a thrown exception:  
        //  
        // value jv = parse( "[1, 2, 3]" );  
        // "  
        //  
        // https://www.boost.org/doc/libs/1_81_0/libs/json/doc/html/json/quick_look.html  
        boost::json::value json_http_request_payload_value = parse(req.body());  
        boost::json::object const& json_http_request_payload = json_http_request_payload_value.as_object();  
          
        // Request:  
        //   
        // {  
        //   "username": "johndoe",  
        //   "password": "super-secret-password"  
        // }  
        auto theUsername = boost::json::value_to<std::string>(json_http_request_payload.at("username"));  
        auto thePassword = boost::json::value_to<std::string>(json_http_request_payload.at("password"));  
      
        // Request path/target must match the API document's "/user/authenticate":  
        //  
        // POST /user/authenticate  
        //  
        if (req.target() == "/user/authenticate")  
        {  
            if (IsUserKnown(theUsername))  
            {  
                // For this particular utility function, it makes sense to return the hash  
                // as well if the password is valid, so that the user can use it later   
                // (if they need to) instead of running the find command and comparison  
                // again. More efficient this way.   
                auto result = IsPasswordValid(theUsername, thePassword);   
                if (result.first)  
                {  
                    // 200 Response:  
                    //   
                    // {  
                    //   "token": "{short-lived-jwt-token}"  
                    // }  
  
                    //const auto now = jwt::date::clock::now();                      
                    const auto now = SystemClock_t::from_time_t(time(NULL));   
                    const auto expiry = now + Seconds_t(JSON_WEB_TOKENS_DURATION_SECONDS);  
                      
                    // ...  
                      
                    //auto token = jwt::create()  
                        //.set_algorithm("RS256")  
                        //.set_type("JWT")  
                        //.set_payload_claim("username", theUsername)  
                        //.set_payload_claim("hashed_password", result.second)  
                        //.set_payload_claim("user_role", "administrator")  
                        //.set_issued_at(now)  
                        //.set_expires_at(expiry)  
                        //.sign(jwt::algorithm::rs256("", RSA_PRIVATE_KEY, "", ""));  
                          
                    auto token = jwt::create().set_issuer("auth0")  
                                     .set_type("JWT")  
                                     .set_id("rsa-create-example")  
                                     .set_issued_at(std::chrono::system_clock::now())  
                                     .set_expires_at(std::chrono::system_clock::now() + std::chrono::seconds{36000})  
                                     .set_payload_claim("sample", jwt::claim(std::string{"test"}))  
                                     .sign(jwt::algorithm::rs256("", RSA_PRIVATE_KEY, "", ""));  
                          
                    std::cout << "\nDebug: Generated token:\n\t" << token << "\n\n";  
                      
                    // In the Boost.JSON library the types array, object, and string  
                    // hold JSON arrays, objects, and strings respectively while the  
                    // type value is a special variant which can hold any JSON element.  
                    //  
                    // Therefore here we construct an empty JSON object representing  
                    // the '200 Response' according to specs. and fill in/insert  
                    // the relevant data that we have elaborately calculated above:  
                    //jwt::traits::boost_json::object response_json; // construct an empty object  
                    boost::json::object response_json; // construct an empty object  
                      
                    //response_json["token"] = token.c_str(); // insert a string  
                    //response_json.insert(std::string("token"), token.c_str());  
                    //response_json.insert(std::string("token"), token);  
                    response_json.at(std::string("token")) = token;  
                      
                    // Per https://www.boost.org/doc/libs/1_81_0/libs/json/doc/html/json/dom/object.html  
                    //  
                    // "Formatted Output  
                    //  
                    // When an 'object' is formatted to a std::ostream, the result   
                    // is a valid JSON. That is, the object will be output with   
                    // curly braces and a comma separated list of key/value pairs,  
                    // as per the JSON specification."  
                    std::ostringstream oss;  
  
                    oss << response_json;  
  
                    std::cout << "\nDebug: response_json:\n\t" << oss.str() << "\n\n";  
                      
                    http::response<http::empty_body> res{http::status::ok, req.version()};  
                    res.set(http::field::server, BOOST_BEAST_VERSION_STRING);  
                    res.set(http::field::content_type, "application/json");  
                    //res.body() = oss.str(); // This line was the one originally not compiling.  
                    res.body() = std::string("Nuertey"); // Neither does this line also compile.  
                    res.content_length(oss.str().size());  
                    res.keep_alive(req.keep_alive());  
                    return res;  
                }  
  
```  
It is probably something simple that I am messing up with with that assignment line of code, taking the string of composed JSON and emplacing it into the body of the HTTP POST Response. Probably, since I may have messed up something else in bringing both of your libraries together. And kindly, if there is a better way I should be using your libraries, do let me know that as well when you have time. Thanks:  
  
```  
                    //res.body() = oss.str(); // This line was the one originally not compiling.  
                    res.body() = std::string("Nuertey"); // Neither does this line also compile.  
```  
Thanks for all the effort and for any insight you can give me.   
Regards, and Happy New Year.  
Nuertey

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-01-25 21:19:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2630#issuecomment-1404237236  

You're using `empty_body` when you should be using `string_body`:  
  
```  
http::response< http::string body > res( http::status::ok, req.version() );  
```

---

## Comment 2

> Username: nuertey  
> Created at: 2023-01-26 07:30:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2630#issuecomment-1404641206  

Excellent @vinniefalco . Thank you. Made the change per your instruction and that helped me progress. My application can compile now; just have one little tiny problem with linking now: "... multiple definition of Boost.JSON artifacts... ". Perhaps I included way too much files from Boost.Asio, Boost.Beast or Boost.JSON. And to reemphasize, I am not using Boost.Asio natively even though that too is an excellent library; I am using Boost.Beast and Boost.JSON and the JWT library. So I think I messed up in including too many header files.  
  
Here is a snippet of the new linking error message:  
  
```  
make  
[ 50%] Built target fmt  
Consolidate compiler generated dependencies of target ...HTTPServer  
[ 66%] Building CXX object CMakeFiles/...HTTPServer.dir/HTTPServer.cpp.o  
[ 83%] Building CXX object CMakeFiles/...HTTPServer.dir/main.cpp.o  
[100%] Linking CXX executable ...HTTPServer  
CMakeFiles/...HTTPServer.dir/main.cpp.o:(.data+0xa7b60): multiple definition of `boost::json::detail::default_resource::instance_'  
CMakeFiles/...HTTPServer.dir/HTTPServer.cpp.o:(.data+0x548a0): first defined here  
CMakeFiles/...HTTPServer.dir/main.cpp.o: In function `boost::system::error_condition::error_condition(boost::system::detail::generic_value_tag)':  
/usr/local/boost_1_81_0/boost/json/impl/array.ipp:46: multiple definition of `boost::json::array::empty_'  
CMakeFiles/...HTTPServer.dir/HTTPServer.cpp.o:/usr/local/boost_1_81_0/boost/json/impl/array.ipp:46: first defined here  
CMakeFiles/...HTTPServer.dir/main.cpp.o: In function `boost::json::array::equal(boost::json::array const&) const':  
/usr/local/boost_1_81_0/boost/json/impl/array.ipp:745: multiple definition of `boost::json::array::equal(boost::json::array const&) const'  
CMakeFiles/...HTTPServer.dir/HTTPServer.cpp.o:/usr/local/boost_1_81_0/boost/json/impl/array.ipp:745: first defined here  
CMakeFiles/...HTTPServer.dir/main.cpp.o: In function `std::ios_base::width(long)':  
/usr/local/boost_1_81_0/boost/json/impl/array.ipp:46: multiple definition of `boost::json::object::empty_'  
CMakeFiles/...HTTPServer.dir/HTTPServer.cpp.o:/usr/local/boost_1_81_0/boost/json/impl/array.ipp:46: first defined here  
CMakeFiles/...HTTPServer.dir/main.cpp.o: In function `boost::json::object::object(std::initializer_list<std::pair<boost::core::basic_string_view<char>, boost::json::value_ref> >, unsigned long, boost::json::storage_ptr)':  
/usr/local/boost_1_81_0/boost/json/impl/object.ipp:368: multiple definition of `boost::json::object::object(std::initializer_list<std::pair<boost::core::basic_string_view<char>, boost::json::value_ref> >, unsigned long, boost::json::storage_ptr)'  
CMakeFiles/...HTTPServer.dir/HTTPServer.cpp.o:/usr/local/boost_1_81_0/boost/json/impl/object.ipp:368: first defined here  
CMakeFiles/...HTTPServer.dir/main.cpp.o: In function `boost::json::object::rehash(unsigned long)':  
/usr/local/boost_1_81_0/boost/json/impl/object.ipp:730: multiple definition of `boost::json::object::rehash(unsigned long)'  
CMakeFiles/...HTTPServer.dir/HTTPServer.cpp.o:/usr/local/boost_1_81_0/boost/json/impl/object.ipp:730: first defined here  
CMakeFiles/...HTTPServer.dir/main.cpp.o: In function `boost::json::object::equal(boost::json::object const&) const':  
/usr/local/boost_1_81_0/boost/json/impl/object.ipp:767: multiple definition of `boost::json::object::equal(boost::json::object const&) const'  
CMakeFiles/...HTTPServer.dir/HTTPServer.cpp.o:/usr/local/boost_1_81_0/boost/json/impl/object.ipp:767: first defined here  
CMakeFiles/...HTTPServer.dir/main.cpp.o: In function `boost::json::detail::string_impl::string_impl()':  
/usr/local/boost_1_81_0/boost/json/detail/impl/string_impl.ipp:33: multiple definition of `boost::json::detail::string_impl::string_impl()'  
  
...  
  
```  
  
And here is my code header inclusion segment pertaining to any Boost artifacts:  
  
```  
#pragma once  
  
#include <boost/asio.hpp>  
#include <boost/bind/bind.hpp>  
#include <boost/asio/io_context.hpp>  
#include <boost/asio/buffer.hpp>  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/dispatch.hpp>  
#include <boost/config.hpp>  
  
#include <boost/json.hpp>  
#include <boost/json/src.hpp>  
  
//#include "jwt-cpp/traits/boost-json/traits.h"  
#include "jwt-cpp/jwt.h"  
  
namespace beast = boost::beast;         // from <boost/beast.hpp>  
namespace http = beast::http;           // from <boost/beast/http.hpp>  
namespace net = boost::asio;            // from <boost/asio.hpp>  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
  
using namespace boost::json;  
```  
  
And since the error message can be, and is quite long, below is the link to the entire thing if that might give any insight. I am very close per your help. I just have to include your headers properly, I think.  
  
https://github.com/nuertey/RandomArtifacts/blob/master/boost_json_linkage_error_message.txt  
  
Thanks  
Nuertey

---

## Comment 3

> Username: vinniefalco  
> Created at: 2023-01-26 23:05:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2630#issuecomment-1405789365  

https://www.boost.org/doc/libs/1_81_0/libs/json/doc/html/json/overview.html#json.overview.requirements.header_only

---

## Comment 4

> Username: nuertey  
> Created at: 2023-02-08 14:24:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2630#issuecomment-1422678493  

Thanks @vinniefalco . I think I understand you. Exactly ONE of the sources has to include the below. Got ya!  
  
```  
#include <boost/json/src.hpp>  
```  
  
A minor question which is connected to this. How do I get at the 'Authorization Bearer Header' value? I tried in two different ways per the below, but obviously I got it incorrect since neither of these two compiled. Thanks.  
  
I tried digging within your code to find out how, but since you used a lot of excellent 'template magic-stuff' :), I could not locate it.   
```  
        // Request path/target must match the API document's "/user/refresh-token".  
        if (req.target() == "/user/refresh-token")  
        {  
            // Per the API document:  
            //  
            // "... The existing token would be validated through the   
            // Authorization header."  
            std::string authorizationHeader;  
            //req.get(http::field::authorization, authorizationHeader);  
            //if (authorizationHeader == g_CurrentlyGeneratedToken)  
            if (req.authorization() == g_CurrentlyGeneratedToken)  
            {  
                // ...  
                  
```  
Regards  
Nuertey

---

## Comment 5

> Username: nuertey  
> Created at: 2023-02-16 02:01:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2630#issuecomment-1432362766  

For other folks that might be having similar issues with the HTTP header, I was able to solve it this way:  
  
```  
    std::string authorizationHeader;  
      
    std::cout << "\nDebug: ALL HTTP Request Header Values:" << "\n\n";  
      
    for (auto& h : req.base())  
    {  
        auto headerValue = std::string(h.value());  
        std::cout << "Header: " << h.name() << ", Header Name: "   
                  << h.name_string() << ", Value: " << headerValue << "\n\n";  
                    
        if (h.name() == http::field::authorization)  
        {  
            authorizationHeader = headerValue;  
            std::cout << "\nDebug: Detected HTTP Request Authorization Header." << "\n\n";  
        }  
    }              
```  
Regards and enjoy.  
Nuertey  
  
P.S. @VinnieFalco you can close this issue. Thanks
