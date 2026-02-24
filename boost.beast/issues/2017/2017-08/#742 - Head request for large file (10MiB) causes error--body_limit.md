# #742 - Head request for large file (10MiB) causes error::body_limit [Closed]

> Username: dirkvdb  
> Created at: 2017-08-15 07:36:09 UTC  
> Updated at: 2017-08-15 21:43:57 UTC  
> Closed at: 2017-08-15 14:30:17 UTC  
> Url: https://github.com/boostorg/beast/issues/742  

When performing a head request on a large file the error::body_limit error gets triggered. This is probably the logic of a get request that checks if the file fits in the body data structure. But it should not happen for head requests.  
  
### Version of Beast  
Current master  
d337339c028f247a91a9b2e771e7b31a085496aa  
  
### Steps necessary to reproduce the problem  
Perform a head request of a large file

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-08-15 13:00:32 UTC  
> Updated at: 2017-08-15 13:02:25 UTC  
> Url: https://github.com/boostorg/beast/issues/742#issuecomment-322459572  

Beast's HTTP operations are "stateless" with respect to successive messages. So the library has no way to know that you sent a HEAD request, and that the response will not have a body. You have to tell Beast not to expect a body. This is done by calling `basic_parser::skip(true)`:  
```  
response_parser<string_body> p;  
  
// Tell the parser not to expect a body  
p.skip(true);  
```  
  
The documentation does not do a good job explaining this, and the example code it provides is wrong (sorry about that). I will update it now, thanks!  
  
I also found a small bug where some `basic_parser` derived class functions like `on_finish` are not called if `skip(true)` is set, I'll fix that too.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-08-15 14:01:49 UTC  
> Url: https://github.com/boostorg/beast/issues/742#issuecomment-322474672  

Please give **version 106** a try:  
https://github.com/boostorg/beast/pull/741  
  
Feel free to leave comments in the Pull Request if you would like to review that code.  
  
Thanks!

---

## Comment 3

> Username: dirkvdb  
> Created at: 2017-08-15 21:43:57 UTC  
> Url: https://github.com/boostorg/beast/issues/742#issuecomment-322598396  

Ok, thanks. Works fine the skip call.
