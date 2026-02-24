# #818 - Problems with fields, does not overwrite them but adds new every update on http-session. [Closed]

> Username: clekby  
> Created at: 2017-10-16 17:49:34 UTC  
> Updated at: 2017-10-17 16:27:55 UTC  
> Closed at: 2017-10-17 16:27:55 UTC  
> Url: https://github.com/boostorg/beast/issues/818  

I can't be certain this is not intentional or that I'm possibly (probably) missing something.  
  
When pressing update in the browser, the server appears to add the same fields to the request that where already present, from previous "GET".   
  
Should I be clearing out req in some way upon sending a response to prevent this from happening ?  
  
**Version of Beast, 122.**  
Boost 1.65  
**Steps necessary to reproduce the problem**  
Easiest to use http_server_async.cpp in examples.  
  
Adding the following below: > path.append("index.html")  
  
`for (auto const& field : req)  
	std::cout << "uri: " << path << ", " << field.name_string() << " = " << field.value() << "\n";  
`  
The content of index.html is not very important, it doesn't even need to exist.  
  
Using a webbrowser to visit the html page on the webserver, and refreshing one or more times.  
  
Example of output from the server, after updating 2 times:  
uri: ./index.html, User-Agent = Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:56.0) Gecko/20100101 Firefox/56.0  
uri: ./index.html, User-Agent = Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:56.0) Gecko/20100101 Firefox/56.0  
uri: ./index.html, User-Agent = Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:56.0) Gecko/20100101 Firefox/56.0  
  
Issue present on:  
Windows 7-64 MSVC 2015 CE, and manually using g++ build on Debian Stretch x86.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-10-16 18:31:18 UTC  
> Updated at: 2017-10-16 18:31:32 UTC  
> Url: https://github.com/boostorg/beast/issues/818#issuecomment-336986059  

Investigating. By the way you could have written `std::cout << req`

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-10-16 18:35:06 UTC  
> Url: https://github.com/boostorg/beast/issues/818#issuecomment-336987411  

Confirmed, this is a bug! Working on fix, thanks!

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-10-16 19:14:58 UTC  
> Url: https://github.com/boostorg/beast/issues/818#issuecomment-337002694  

Please try **Version 123** and let me know, it should fix this issue: https://github.com/boostorg/beast/pull/815

---

## Comment 4

> Username: clekby  
> Created at: 2017-10-17 06:59:11 UTC  
> Url: https://github.com/boostorg/beast/issues/818#issuecomment-337136640  

adding m_.clear() to parser.ipp breaks the build for me,   
**Error	C2248	'boost::beast::http::basic_fields<std::allocator<char>>::clear': cannot access private member declared in class 'boost::beast::http::basic_fields<std::allocator<char>>'	http-server-async	C:\Development\boost-1.65-1\libs\beast\include\boost\beast\http\impl\parser.ipp	35	  
**  
  
publishing the void clear() function in fields.hpp, clears that up and apparently solves the issue.

---

## Comment 5

> Username: rejjen  
> Created at: 2017-10-17 08:24:47 UTC  
> Url: https://github.com/boostorg/beast/issues/818#issuecomment-337156245  

Well, apparently didn't read the commit correctly, and missed the delete's in fields.hpp.  
Disregard the prior comment.  
Sorry...I did try to pull the changes, but being somewhat of a newbie in the ways of github and git and c++ makes everything just on a steeply slanted learning curve. And getting work done, slow.  
  
Thank you.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-10-17 15:30:37 UTC  
> Url: https://github.com/boostorg/beast/issues/818#issuecomment-337270881  

Okay, so does version 123 resolve this issue?

---

## Comment 7

> Username: clekby  
> Created at: 2017-10-17 16:27:55 UTC  
> Url: https://github.com/boostorg/beast/issues/818#issuecomment-337288092  

Indeed.
