# #1748 - issue using http::request<http::buffer_body> with http::async_write [Closed]

> Username: haashirashraf  
> Created at: 2019-10-29 22:21:57 UTC  
> Updated at: 2019-10-30 10:28:24 UTC  
> Closed at: 2019-10-30 10:28:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1748  

Firstly thanks a lot guys, its probably something simple but when using beast 1.70.0 I initialise the buffer_body with a char* to an array and initialise its size to the number of bytes in the array (I am sure that this array is initialised) when I attempt to write this http::request<http::buffer_body> using http::async_write to a beast::ssl_stream I get the error need buffer in my on_write handler, why is this?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-10-29 23:26:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1748#issuecomment-547673516  

What do the docs say about this?

---

## Comment 2

> Username: haashirashraf  
> Created at: 2019-10-29 23:55:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1748#issuecomment-547679871  

The docs say that all members of http::buffer_body::value_type must be initialised, I didn't mention the bool more value which defaults to true, so that is to say this should be set to false?  
  
I see the need buffer error is raised when the data member of the buffer body is a nullptr, I'll try again and set this to false!  
  
Actually I think this will work because I recall seeing (nil) when printing the bodies of my keep alive requests which initialised data to nullptr and prior to doing this seeing the need buffer error!

---

## Comment 3

> Username: haashirashraf  
> Created at: 2019-10-30 10:28:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1748#issuecomment-547834545  

That fixed it! Thanks a lot Vinny!
