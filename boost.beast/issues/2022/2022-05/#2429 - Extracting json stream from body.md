# #2429 - Extracting json stream from body [Closed]

> Username: jade2k11598  
> Created at: 2022-05-21 03:21:10 UTC  
> Updated at: 2022-05-21 16:33:29 UTC  
> Closed at: 2022-05-21 16:31:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2429  

Hi,  
I'm expecting a json stream in the http body response.  But I'm not sure how to extract this into a `boost::property_tree::ptree`.   I did see[ this link](https://www.boost.org/doc/libs/1_78_0/libs/beast/doc/html/beast/using_http/custom_body_types.html) about custom body and the example shown uses the boost property_tree.    
  
  
  
So I have something like this:  
```  
struct JsonBody  
{  
    using value_type = boost::property_tree::ptree;  
    class reader;  
    class writer;  
};  
  
...  
    // This buffer is used for reading and must be persisted  
    beast::flat_buffer buffer;  
  
    // Declare a container to hold the response  
    http::response<JsonBody> res;  
  
    // Receive the HTTP response  
    auto sz = http::read(_stream, buffer, res);  
  
    auto rc = res.result();  
    if (rc != http::status::ok)  
    {  
        std::cerr << "Error, got httpcode:" << rc << std::endl;  
        return;  
    }  
  
    // body here should be a boost property_tree p since used JsonBody when  
    // declaring res.  
    auto body = res.body();  
    ...  
```  
  
But I'm getting a compilation error:  
```  
In file included from /Users/jade/thirdParty/boost_1_78_0/build-artifacts/include/boost/beast/http/read.hpp:824:  
/Users/jade/thirdParty/boost_1_78_0/build-artifacts/include/boost/beast/http/impl/read.hpp:638:5: error: static_assert failed due to requirement 'is_body_reader<JsonBody, void>::value' "BodyReader type requirements not met"  
    static_assert(is_body_reader<Body>::value,  
    ^             ~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/Users/jade/debugging/Broad-test/client-get.cpp:143:21: note: in instantiation of function template specialization 'boost::beast::http::read<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp>>, boost::beast::basic_flat_buffer<std::allocator<char>>, false, JsonBody, std::allocator<char>>' requested here  
    auto sz = http::read(_stream, buffer, res);  
                    ^  
In file included from /Users/jade/debugging/Broad-test/client-get.cpp:7:  
In file included from /Users/jade/debugging/Broad-test/client-get.hpp:3:  
In file included from /Users/jade/thirdParty/boost_1_78_0/build-artifacts/include/boost/beast/http.hpp:28:  
In file included from /Users/jade/thirdParty/boost_1_78_0/build-artifacts/include/boost/beast/http/read.hpp:824:  
/Users/jade/thirdParty/boost_1_78_0/build-artifacts/include/boost/beast/http/impl/read.hpp:667:5: error: static_assert failed due to requirement 'is_body_reader<JsonBody, void>::value' "BodyReader type requirements not met"  
    static_assert(is_body_reader<Body>::value,  
    ^             ~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/Users/jade/thirdParty/boost_1_78_0/build-artifacts/include/boost/beast/http/impl/read.hpp:642:15: note: in instantiation of function template specialization 'boost::beast::http::read<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp>>, boost::beast::basic_flat_buffer<std::allocator<char>>, false, JsonBody, std::allocator<char>>' requested here  
        http::read(stream, buffer, msg, ec);  
              ^  
/Users/jade/debugging/Broad-test/client-get.cpp:143:21: note: in instantiation of function template specialization 'boost::beast::http::read<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp>>, boost::beast::basic_flat_buffer<std::allocator<char>>, false, JsonBody, std::allocator<char>>' requested here  
    auto sz = http::read(_stream, buffer, res);  
                    ^  
In file included from /Users/jade/debugging/Broad-test/client-get.cpp:7:  
In file included from /Users/jade/debugging/Broad-test/client-get.hpp:3:  
In file included from /Users/jade/thirdParty/boost_1_78_0/build-artifacts/include/boost/beast/http.hpp:27:  
/Users/jade/thirdParty/boost_1_78_0/build-artifacts/include/boost/beast/http/parser.hpp:55:5: error: static_assert failed due to requirement 'is_body_reader<JsonBody, void>::value' "BodyReader type requirements not met"  
    static_assert(is_body_reader<Body>::value,  
    ^             ~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/Users/jade/thirdParty/boost_1_78_0/build-artifacts/include/boost/beast/http/impl/read.hpp:669:40: note: in instantiation of template class 'boost::beast::http::parser<false, JsonBody>' requested here  
    parser<isRequest, Body, Allocator> p(std::move(msg));  
                                       ^  
/Users/jade/thirdParty/boost_1_78_0/build-artifacts/include/boost/beast/http/impl/read.hpp:642:15: note: in instantiation of function template specialization 'boost::beast::http::read<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp>>, boost::beast::basic_flat_buffer<std::allocator<char>>, false, JsonBody, std::allocator<char>>' requested here  
        http::read(stream, buffer, msg, ec);  
              ^  
/Users/jade/debugging/Broad-test/client-get.cpp:143:21: note: in instantiation of function template specialization 'boost::beast::http::read<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp>>, boost::beast::basic_flat_buffer<std::allocator<char>>, false, JsonBody, std::allocator<char>>' requested here  
    auto sz = http::read(_stream, buffer, res);  
                    ^  
3 errors generated.  
  
```  
  
I'm guessing my `struct JsonBody` is missing something, though I thought I followed the example in the referenced link.  Is there an example somewhere on how to read the body into a `boost::property_tree::ptree`?  
  
I'm using boost beast version is 1.78

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-05-21 07:03:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2429#issuecomment-1133551482  

Writing an incremental ptree parser would not be trivial. If you want quick results, my advice would be to use a `string_body` and then parse the `body()` into the ptree (or `boost::json::value`) once the read is complete.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-05-21 13:12:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2429#issuecomment-1133632166  

Don't use PropertyTree, use Boost.JSON instead, as it is real JSON.

---

## Comment 3

> Username: jade2k11598  
> Created at: 2022-05-21 15:59:55 UTC  
> Updated at: 2022-05-21 16:00:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2429#issuecomment-1133660390  

Thanks, the `string_body` worked fine putting into the Boost.JSON object.  
  
I have one final question and I don't know if this is really a RESTful API protocol question and not a beast related question, but  if one makes a GET request for a list of items (json formatted), not knowing how long that list is, how does one manage it, such that the `string_body` doesn't get enormously large, to avoid running out of heap memory?  Is there something in the GET protocol that I should ask the server to send me the list in batches of let's say 10 at a time to process (though not quite sure what the protocol is for getting data in batches)?

---

## Comment 4

> Username: jade2k11598  
> Created at: 2022-05-21 16:31:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2429#issuecomment-1133665097  

Never mind, I think I realized there isn't any generic means of requesting a 'GET in batches'.  It seems the only way to do this is to add a query to the GET that manages the range of the list.  To get the full list one would then have to do this in a series of GETs.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-05-21 16:33:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2429#issuecomment-1133665405  

> add a query to the GET that manages the range of the list  
  
Yeah. And to prevent resource exhaustion attacks, you can set a limit on the maximum size of the received body.
