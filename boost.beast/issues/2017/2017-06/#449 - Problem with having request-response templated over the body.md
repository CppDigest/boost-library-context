# #449 - Problem with having request/response templated over the body [Closed]

> Username: niklas88  
> Created at: 2017-06-09 08:12:27 UTC  
> Updated at: 2022-02-20 07:08:49 UTC  
> Closed at: 2017-06-10 00:59:01 UTC  
> Url: https://github.com/boostorg/beast/issues/449  

Having the request/response be a template over its body type means I can't decide what type of body is requested based on the request itself.  
  
I'm trying to wrap Beast in a higher-level architecture (somewhat loosely inspired by http://golang.org/pkg/http) which allows setting handlers for request paths. For example one should be able to handle GETs to "/api/v1/sqrt?q=5" with a handler using JSON e.g. as beast::http::request<beast::http::string_body>. While  
PUTs to "/upload" should be handled via file_body. Having requests templated on the body means that I can't match paths to handlers with different body types at runtime.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-09 11:56:00 UTC  
> Url: https://github.com/boostorg/beast/issues/449#issuecomment-307369542  

Have a little more faith :)  
http://vinniefalco.github.io/beast/beast/http_examples/defer_body_type.html

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-09 13:09:05 UTC  
> Url: https://github.com/boostorg/beast/issues/449#issuecomment-307383926  

That Go http package looks very nice! You might want to also take a look at this:  
https://github.com/whoshuu/cpr  
  
I'm very interested to see people build easy to use clients (and servers) on top of Beast!

---

## Comment 3

> Username: niklas88  
> Created at: 2017-06-09 14:04:12 UTC  
> Url: https://github.com/boostorg/beast/issues/449#issuecomment-307397728  

Thanks for the hint for delayed body types 👍 Yes I know cpr, though last I checked it wrapped libcurl which I always found clunky to use. Having HTTP in the standard library in Go is really nice, I feel like the C++ committee really hurt the language in the past when it comes to having a useful standard library.   
  
For the longest time they were so obsessed with supporting every fringe use case (e.g. char not being 8-bit, to hell with those architectures - let them use assembly or their own shitty language) that it really hurt the use of the language in the common case. With the addition of std::thread/std::mutex etc. this attitude seems to be changing but I think in the end it made C++ much less portable, less productive and a lot more frustrating to use.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-06-09 14:14:25 UTC  
> Url: https://github.com/boostorg/beast/issues/449#issuecomment-307400437  

Couldn't agree more!  
  
About cpr, yes libcurl is clunky. But I like his design of the user-facing interface. It is something that I might have to steal some day if no one beats me to it!

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-06-09 14:15:03 UTC  
> Updated at: 2017-06-09 14:15:14 UTC  
> Url: https://github.com/boostorg/beast/issues/449#issuecomment-307400608  

>delayed body types  
  
That's an interesting choice of word. The documentation calls it "deferred" but maybe "delayed" is better? Although, someone might get confused and think it is the kind of body that comes in more slowly from the network...

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-06-10 00:59:01 UTC  
> Url: https://github.com/boostorg/beast/issues/449#issuecomment-307531582  

Feel free to re-open or file a new issue as needed, thanks!

---

## Comment 7

> Username: darkomenz  
> Created at: 2022-02-20 05:38:43 UTC  
> Url: https://github.com/boostorg/beast/issues/449#issuecomment-1046167135  

Found a 404 (dead link): http://vinniefalco.github.io/beast/beast/http_examples/defer_body_type.html

---

## Comment 8

> Username: vinniefalco  
> Created at: 2022-02-20 07:08:49 UTC  
> Url: https://github.com/boostorg/beast/issues/449#issuecomment-1046177035  

Official documentation link: https://www.boost.org/doc/libs/1_78_0/libs/beast/doc/html/beast/more_examples/change_body_type.html
