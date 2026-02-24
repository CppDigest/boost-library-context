# #1909 - SSL connection and problems with paths [Closed]

> Username: FabrizioDys  
> Created at: 2020-04-21 09:58:11 UTC  
> Updated at: 2020-04-22 09:58:15 UTC  
> Closed at: 2020-04-22 09:58:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1909  

using Boost 1.71  
  
Hi,  
I recently added Boost 1.71 to the code I'm working on. I am trying to download a file via HTTP GET request, but since the site from where I have to download it is secure I am using the SSL code given by you.  
I have two major problems:  
1- If I try to insert as the input parameter anything that has a path other than the host I get "couldn't resolve host", e.g. "liris.cnrs.fr" is working ( or at least it doesn't report THIS kind of error ) but "liris.cnrs.fr/xyz.txt" results is host not resolved.  
I looked at how the codes are done inside the "http_client_async_ssl" and I found out that the object req_ had a method to set another object called "http::field". In this http::field there was also a "path" so I did something like this:  
  
` auto const host = argv[1];  
    auto const port = argv[2];  
    auto const target = argv[3];  
    auto const path = argv[4];  
    int version = argc == 5 && !std::strcmp("1.0", argv[5]) ? 10 : 11;`  
...  
`std::make_shared<session>(  
        net::make_strand(ioc),  
        ctx  
        )->run(host, port, target,path, version);`  
...[inside run]...  
` req_.version(version);  
        req_.method(http::verb::get);  
        req_.target(target);  
        req_.set(http::field::host, host);  
        req_.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
        req_.set(http::field::path, path);`  
  
But I believe it can't be that easy.. Can you help me out?  
  
2- If I try to connect to a simple host without paths, e.g. "liris.cnrs.fr" the other problem is that the SSL certificate is not valid: "Handshake: certificate verify failed"

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-04-21 10:21:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1909#issuecomment-617091270  

HI @FabrizioDys ,  
  
It's exactly as easy as you think.  
  
The specification for URLs is more complex than you might imagine, but for our purposes, it boils down to:  
  
https:// [the scheme, which also implies a port] www.example.com [the host] /foo/bar.html?answer=45 [the target]  
  
The http_client_async_ssl example shows this:  
  
https://www.boost.org/doc/libs/1_72_0/libs/beast/example/http/client/async-ssl/http_client_async_ssl.cpp

---

## Comment 2

> Username: FabrizioDys  
> Created at: 2020-04-21 10:27:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1909#issuecomment-617093975  

Hi @madmongo1 .  
thank you so much for your response, the first problem is solved :-)  
  
For what concerns "handshake: certificate verify failed" do you have any clue on how to solve that? Reading the header file given by Boost example I should be able to consult my specific certificate inside my operative system but I'm not sure how to access that instead of the sample one ( "root_certificates.hpp" )
