# #90 - Problem with Example: error: conversion from ‘boost::urls::result<boost::urls::url_view>’ {aka ‘boost::system::result<boost::urls::url_view, boost::system::error_code>’} to non-scalar type ‘boost::urls::url_view’ requested [Closed]

> Username: WarrenN1  
> Created at: 2021-12-23 00:19:39 UTC  
> Updated at: 2022-02-01 02:19:03 UTC  
> Closed at: 2022-02-01 02:19:02 UTC  
> Url: https://github.com/boostorg/url/issues/90  

I thought I would move from the beast issues tab to here given this is more library specific.  
Boost 1.78.0  
  
Building:  
Upon building I moved all the files under: url-master/include/boost/ to boost_1_78_0/boost/  
I then proceeded with bootstrapping and then making boost  
Here is the full Error Message:  
```  
error: conversion from ‘boost::urls::result<boost::urls::url_view>’ {aka ‘boost::system::result<boost::urls::url_view, boost::system::error_code>’} to non-scalar type ‘boost::urls::url_view’ requested  
     url_view uv = parse_uri("https://user:pass@www.example.com:443/path/to/my%2dfile.txt?id=42&name=John%Doe#anchor");  
```  
Here is the code:  
```  
#include <boost/config.hpp>  
#include <boost/url/src.hpp>  
using namespace boost::urls;  
  
url_view uv = parse_uri("https://user:pass@www.example.com:443/path/to/my%2dfile.txt?id=42&name=John%Doe#anchor");  
```  
  
Reading through the documentation/examples this seems like it should work, but when I tried the sample code it failed. I was wondering where I might have gone wrong and or if the docs could not reflect the latest best practice?

---

## Comment 1

> Username: WarrenN1  
> Created at: 2021-12-23 01:36:29 UTC  
> Url: https://github.com/boostorg/url/issues/90#issuecomment-999980340  

Resolved: remove leading namespace from boost::urls;

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-12-23 02:18:25 UTC  
> Url: https://github.com/boostorg/url/issues/90#issuecomment-999993850  

`parse_uri` returns `result<url_view>` not `url_view`.

---

## Comment 3

> Username: WarrenN1  
> Created at: 2021-12-23 22:00:01 UTC  
> Url: https://github.com/boostorg/url/issues/90#issuecomment-1000540180  

> result<url_view>  
  
How should I update the example docs to reflect this given the example simply uses url_view?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-12-24 02:58:17 UTC  
> Url: https://github.com/boostorg/url/issues/90#issuecomment-1000622876  

> How should I update the example docs to reflect this given the example simply uses url_view?  
  
Where is the example?

---

## Comment 5

> Username: alandefreitas  
> Created at: 2021-12-24 02:59:32 UTC  
> Url: https://github.com/boostorg/url/issues/90#issuecomment-1000623213  

@WarrenN1   
  
The [result<T>](https://www.boost.org/doc/libs/1_78_0/libs/system/doc/html/system.html#usage_resultt_as_an_alternative_to_dual_apis) class comes from the boost.system library. It's a cheaper alternative to exceptions and a less verbose alternative than passing an error output parameter. It's very similar to a `std::variant<value, error_code>`.  
  
So in a `result<url_view> r`, you can just call `r.value()` to get the internal `url_view`. You can also call `r.has_value()` to check if it didn't fail to parse and `r.error()` to get the error code if it failed to parse. This means if you're not using all the `result` functionality, the example would just become:  
  
```cpp  
url_view uv = parse_uri("https://user:pass@www.example.com:443/path/to/my%2dfile.txt?id=42&name=John%Doe#anchor").value();  
```

---

## Comment 6

> Username: alandefreitas  
> Created at: 2021-12-24 16:28:02 UTC  
> Url: https://github.com/boostorg/url/issues/90#issuecomment-1000892685  

@WarrenN1   
  
Where did you read this example  
  
```cpp  
url_view uv = parse_uri("https://user:pass@www.example.com:443/path/to/my%2dfile.txt?id=42&name=John%Doe#anchor");  
```  
  
without the `.value()` suffix?  
  
I'm trying to fix it but https://master.url.cpp.al/url/quick_look.html says  
  
```cpp  
url_view uv = parse_uri("https://user:pass@www.example.com:443/path/to/my%2dfile.txt?id=42&name=John%Doe#anchor").value();  
```  
  
with the `.value()` suffix as it should. Is it somewhere else?

---

## Comment 7

> Username: WarrenN1  
> Created at: 2021-12-24 20:47:14 UTC  
> Url: https://github.com/boostorg/url/issues/90#issuecomment-1000928908  

The example docs work (I clearly just goofed it). I was just wondering how (if at all) we should update them to add if else error catching to the example given that in production most people could need to catch errors on result like bad alpha.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2021-12-24 20:55:43 UTC  
> Url: https://github.com/boostorg/url/issues/90#issuecomment-1000930026  

> I was just wondering how (if at all) we should update them to add if else error catching to the example  
  
Isn't this shown on the first code sample of this page? https://master.url.cpp.al/url/parsing.html

---

## Comment 9

> Username: alandefreitas  
> Created at: 2021-12-24 21:01:58 UTC  
> Url: https://github.com/boostorg/url/issues/90#issuecomment-1000930831  

You can also see how boost.system result works in general in the link I sent you before:  
  
https://www.boost.org/doc/libs/1_78_0/libs/system/doc/html/system.html#usage_resultt_as_an_alternative_to_dual_apis

---

## Comment 10

> Username: vinniefalco  
> Created at: 2022-02-01 02:19:02 UTC  
> Url: https://github.com/boostorg/url/issues/90#issuecomment-1026416042  

I don't see any remaining problems so I am going to close this
