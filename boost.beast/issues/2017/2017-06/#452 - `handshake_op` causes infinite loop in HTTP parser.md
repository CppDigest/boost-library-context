# #452 - `handshake_op` causes infinite loop in HTTP parser [Closed]

> Username: harrishancock  
> Created at: 2017-06-09 20:08:02 UTC  
> Updated at: 2017-06-09 22:07:48 UTC  
> Closed at: 2017-06-09 22:07:48 UTC  
> Url: https://github.com/boostorg/beast/issues/452  

It is possible for `async_handshake()` to cause an infinite loop in `basic_parser::put()`. This is a regression introduced by commit fe75a7ca2, according to my git-bisect, and it affects the master branch.  
  
Here is code to reproduce on gcc 5.4:  
  
```c++  
#include <beast/websocket.hpp>  
#include <iostream>  
  
int main() {  
    boost::asio::io_service context;  
    boost::asio::ip::tcp::endpoint server_ep{  
        boost::asio::ip::address::from_string("0.0.0.0"), 1234  
    };  
    boost::asio::ip::tcp::acceptor acceptor{context, server_ep};  
    beast::websocket::stream<boost::asio::ip::tcp::socket> cl{context}, sv{context};  
  
#define CHECK(x) if (x) { std::cerr << x.message() << '\n'; return; }  
  
    acceptor.async_accept(sv.next_layer(), [&](const beast::error_code& ec) {  
        CHECK(ec);  
        sv.async_accept([&](const beast::error_code& ec) {  
            CHECK(ec);  
            sv.set_option(beast::websocket::message_type{beast::websocket::opcode::binary});  
            // Removing this `async_write()` call prevents the infinite loop on my machine.  
            sv.async_write(boost::asio::buffer("die!"), [&](const beast::error_code& ec) {  
                CHECK(ec);  
            });  
        });  
    });  
  
    cl.next_layer().async_connect(server_ep, [&](const beast::error_code& ec) {  
        CHECK(ec);  
        cl.async_handshake("foo.com", "/", [&](const beast::error_code& ec) {  
            CHECK(ec);  
            BOOST_ASSERT(false);  // never reached!  
        });  
    });  
  
    context.run();  
}  
```  
  
Note that the server calls `async_write()` in its `async_accept()` callback. If I remove that call, the infinite loop disappears, and the `BOOST_ASSERT()` line triggers.  
  
This is what the infinite loop looks like in gdb. The line numbers are accurate to commit a2c1117f.  
  
```  
0x000000000044eb7f in beast::http::basic_parser<false, beast::http::parser<false, beast::http::string_body, beast::http::basic_fields<std::allocator<char> > > >::put (this=0x920c50, buffer=..., ec=...)  
    at /home/harris/build-core/deps/daemon/include/beast/http/impl/basic_parser.ipp:173  
173	    if(p < p1 && eager())  
(gdb) s  
beast::http::basic_parser<false, beast::http::parser<false, beast::http::string_body, beast::http::basic_fields<std::allocator<char> > > >::eager (this=0x920c50)  
    at /home/harris/build-core/deps/daemon/include/beast/http/basic_parser.hpp:241  
241	        return (f_ & flagEager) != 0;  
(gdb)   
242	    }  
(gdb)   
beast::http::basic_parser<false, beast::http::parser<false, beast::http::string_body, beast::http::basic_fields<std::allocator<char> > > >::put (this=0x920c50, buffer=..., ec=...)  
    at /home/harris/build-core/deps/daemon/include/beast/http/impl/basic_parser.ipp:175  
175	        n = static_cast<std::size_t>(p1 - p);  
(gdb)   
176	        goto loop;  
(gdb)   
108	    switch(state_)  
(gdb)   
171	        break;  
(gdb)   
173	    if(p < p1 && eager())  
(gdb) print state_  
$1 = beast::http::detail::basic_parser_base::state::complete  
(gdb) print p  
$2 = 0x920dcb "\202\005die!"  
(gdb) print p1  
$3 = 0x920dd2 "G"  
(gdb) print eager()  
$4 = true  
(gdb)   
```  
  
Note the state of the variables I printed out: there is data in the buffer, the parser is eager, but complete.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-09 20:22:26 UTC  
> Url: https://github.com/boostorg/beast/issues/452#issuecomment-307490439  

Thanks for the detailed bug report, investigating...

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-09 20:34:45 UTC  
> Updated at: 2017-06-09 20:34:58 UTC  
> Url: https://github.com/boostorg/beast/issues/452#issuecomment-307493060  

Yep, it was exactly what you described (thanks for the detailed report). This function reproduces the defect:  
```  
void testRegression452()  
{  
    error_code ec;  
    test_parser<true> p;  
    p.eager(true);  
    string_view s =  
        "GET / HTTP/1.1\r\n"  
        "\r\n"  
        "die!"  
        ;  
    p.put(boost::asio::buffer(  
        s.data(), s.size()), ec);  
    pass();  
}  
```  
  
This is the fix: https://github.com/vinniefalco/Beast/commit/cafc8e25c245efa85078e12c6b155adbf2c3eb8f it will go into **v51**

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-06-09 20:42:10 UTC  
> Url: https://github.com/boostorg/beast/issues/452#issuecomment-307494595  

Considering the magnitude of changes in a short time, I'd say we got off easy!

---

## Comment 4

> Username: harrishancock  
> Created at: 2017-06-09 20:50:18 UTC  
> Url: https://github.com/boostorg/beast/issues/452#issuecomment-307496369  

Fantastic, I'm relieved it was an easy fix!
