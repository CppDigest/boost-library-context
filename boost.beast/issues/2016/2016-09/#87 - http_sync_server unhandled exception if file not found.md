# #87 - http_sync_server unhandled exception if file not found [Closed]

> Username: e-fominov  
> Created at: 2016-09-24 13:12:17 UTC  
> Updated at: 2016-09-25 16:21:01 UTC  
> Closed at: 2016-09-25 16:21:01 UTC  
> Url: https://github.com/boostorg/beast/issues/87  

Hello, I have found Beast as an amazing implementation of C++ embeddable web-server.   
http_sync_server sample crashes if file not found with throwing unhandled exception boost::system_error  
With debugger I have found two problems:  
1. We need to put the code of sending file into `else` branch of [`if(! boost::filesystem::exists(path))`](https://github.com/vinniefalco/Beast/blob/master/examples/http_sync_server.hpp#L162) here   
   Because if file was not found - there will be sent 404 error and program will continue trying to send file with 200 result code  
2. Inside [`catch(std::exception const& e)  
           {  
               res = {};`](https://github.com/vinniefalco/Beast/blob/master/examples/http_sync_server.hpp#L187)  
   handler we need to costruct resposne with string_body, because it tries to put `std::string{"An internal error occurred"} + e.what();` into file body and crashes with preparing this response

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-09-25 13:11:33 UTC  
> Url: https://github.com/boostorg/beast/issues/87#issuecomment-249421162  

You're right!

---

## Comment 2

> Username: vinniefalco  
> Created at: 2016-09-25 16:21:01 UTC  
> Url: https://github.com/boostorg/beast/issues/87#issuecomment-249430817  

Thanks for these fixes. I've applied your changes to the async server as well, and this is part of b14 which should come out this week. Here's the branch:  
https://github.com/vinniefalco/Beast/commits/b14
