# #11 - parse with error_code [Closed]

> Username: vinniefalco  
> Created at: 2020-01-23 22:12:14 UTC  
> Updated at: 2021-09-14 04:48:25 UTC  
> Closed at: 2021-09-14 04:48:25 UTC  
> Url: https://github.com/boostorg/url/issues/11  

`view::view( string_view s, error_code& ec)`  
  
and  
  
`basic_value::basic_value( string_view s, error_code& ec )`  
  
can leave the URL empty on error

---

## Comment 1

> Username: syoliver  
> Created at: 2020-03-29 14:08:57 UTC  
> Url: https://github.com/boostorg/url/issues/11#issuecomment-605641947  

@vinniefalco Your url implementation is really interesting, but would you think possible to have a separate function for parsing urls or maybe something like a named constructor ?  
I think it would improve readability.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-03-29 14:34:07 UTC  
> Url: https://github.com/boostorg/url/issues/11#issuecomment-605645981  

Yeah, I will have that eventually, mind you that this library is still being worked on (I'm busy with Boost.JSON at the moment: https://github.com/CPPAlliance/json  
  
Thanks!

---

## Comment 3

> Username: syoliver  
> Created at: 2020-03-30 06:43:45 UTC  
> Url: https://github.com/boostorg/url/issues/11#issuecomment-605813295  

Yes I know about json library, but I would like to help on url if possible, without conflicting with what you have already done or what you have in mind.  
This issue seems easy to begin with and try to propose something.

---

## Comment 4

> Username: edtanous  
> Created at: 2020-06-24 19:33:25 UTC  
> Updated at: 2020-06-24 19:40:19 UTC  
> Url: https://github.com/boostorg/url/issues/11#issuecomment-649023740  

+1  Those two prototypes would be very useful for codebases with -fno-exceptions set.  I tried to integrate this library into something I've been working on and the inability to capture an error in control flow when translating an unverified target string to a url is about the only thing holding me back from being able to use this in my use.  For the golden paths, this library seems to work fantastically as intended.  
  
```c++  
// To add more detail.  This is the code I have to write today:  
boost::beast::http::request</*Custom Body*/> request;  
boost::urls::url url(request.target());  // this might throw to a global BOOST_THROW_EXCEPTION define that is currently mapped to std::terminate  
  
  
// The code I'd like to be able to write  
boost::beast::http::request</*Custom Body*/> request;  
boost::urls::url url;  
if (!url.parse(request.target())){  
    // handle error (close connection, return 400, ect)  
    return;  
}  
```

---

## Comment 5

> Username: vinniefalco  
> Created at: 2021-09-14 04:48:25 UTC  
> Url: https://github.com/boostorg/url/issues/11#issuecomment-918798415  

> the inability to capture an error in control flow when translating an unverified target string to a url is about the only thing holding me back from being able to use this in my use  
  
This should be completely fixed now, there's a full suite of parse functions which use errors. Furthermore, you can manipulate URLs without any memory allocations using `static_url`.
