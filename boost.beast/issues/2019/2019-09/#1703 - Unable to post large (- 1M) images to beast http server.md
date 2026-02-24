# #1703 - Unable to post large (> 1M) images to beast http server [Closed]

> Username: mzimbres  
> Created at: 2019-09-13 22:33:48 UTC  
> Updated at: 2019-09-13 23:47:27 UTC  
> Closed at: 2019-09-13 23:47:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1703  

Hi,  
  
I not being able to upload large (> ~1M) images to a http server built  
with beast. My server performs the following steps  
  
  1. Reads the http headers using ```http::async_read_header``` and the  
     parser ```http::request_parser<http::empty_body>```  
  
  2. Moves the headers into a ```http::request_parser<http::file_body>```  
     and proceeds reading the body with ```http::async_read```.  
  
The images are post'ed from a flutter application that uses a dart  
http client running on android.  As long as the image is relatively  
small, this works well. However for moderately large images, beast  
reports error::bad_file. Notice I am setting the body limit to a large  
value, i.e.   
  
   ```header_parser.body_limit(10000000);```  
  
For the small image I see the following sequence   
  
   User-Agent = Dart/2.5 (dart:io)  
   Accept-Encoding = gzip  
   Content-Length = 295209  
   Host = 37.24.165.216:81  
  
   ===> on_read reports a read with size: 295209. Ok  
  
For the larger image I see the following  
  
   User-Agent = Dart/2.5 (dart:io)  
   Accept-Encoding = gzip  
   Content-Length = 1634581  
   Host = 37.24.165.216:81  
   ===> on_read reports a read with size: 512 (Why?)  
   Error: bad field  
  
I also tried to check that the client is indeed sending the correct  
content with nc. I see the following for the small image  
  
   $ sudo nc -l -p 81 > foo.txt  
   $ du -h foo.txt   
   292K    foo.txt <==== Correct  
   $ cat foo.txt   
  
   POST /images/rRz1A.jpg HTTP/1.1  
   user-agent: Dart/2.5 (dart:io)  
   accept-encoding: gzip  
   content-length: 295209  
   host: 37.24.165.216:81  
     
   ===> Binary data.  
  
For the larger file I see  
  
   $ sudo nc -l -p 81 > foo.txt  
   $ du -h foo.txt   
   1.6M    foo.txt <==== Also correct  
   $ cat foo.txt   
  
   POST /images/+jHQP.jpg HTTP/1.1  
   user-agent: Dart/2.5 (dart:io)  
   accept-encoding: gzip  
   content-length: 1634581  
   host: 37.24.165.216:81  
     
   ===> Binary data.  
  
That means the client is sending the complete body correctly but I am  
unable to understand why beast reports error::bad_field. I also tried  
to understand in http/detail/basic_parser.ipp why that happens but so  
far without success.  
  
Best regards,  
Marcelo

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-09-13 22:40:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1703#issuecomment-531412602  

How exactly do you "Moves the headers into a `http::request_parser<http::file_body>`" ?

---

## Comment 2

> Username: XCaminhante  
> Created at: 2019-09-13 22:40:52 UTC  
> Updated at: 2019-09-13 22:41:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1703#issuecomment-531412700  

> ![image](https://user-images.githubusercontent.com/46259/64898608-222aa200-d65e-11e9-959c-0fc6268f171d.png)  
  
Just no.

---

## Comment 3

> Username: mzimbres  
> Created at: 2019-09-13 23:36:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1703#issuecomment-531422127  

> How exactly do you "Moves the headers into a `http::request_parser<http::file_body>`" ?  
  
As far as I can see, the ```request_parser``` does not provide proper  
move semantics, I use a therefore a ```std::unique_ptr```  
  
```  
   using req_body_parser_type = http::request_parser<file_body_type>;  
  
   std::unique_ptr<req_body_parser_type> body_parser;  
```  
  
After reading the headers, I move the header parser into the  
body_parser.  
  
```  
   body_parser = std::make_unique< req_body_parser_type  
                                 >(std::move(header_parser));  
  
   // After that I read the body  
  
   auto self = shared_from_this();  
   auto f = [self](auto ec, auto n)  
      { self->on_read_post_body(ec, n); };  
  
   http::async_read(socket, buffer, *body_parser, f);  
```

---

## Comment 4

> Username: mzimbres  
> Created at: 2019-09-13 23:47:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1703#issuecomment-531423607  

Hi Vinnie, That was my fault, I was calling body_limit after reading the header, I moved it to before reading the header and it is working now. I believe this is the correct way to work with the parser. Sorry for the noise, you can close this issue. By the way, Beast has a very good quality.
