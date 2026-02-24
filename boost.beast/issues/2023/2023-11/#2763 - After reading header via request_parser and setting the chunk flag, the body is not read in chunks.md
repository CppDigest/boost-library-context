# #2763 - After reading header via request_parser and setting the chunk flag, the body is not read in chunks [Closed]

> Username: JustinKyleJames  
> Created at: 2023-11-02 23:02:13 UTC  
> Updated at: 2023-11-03 00:22:07 UTC  
> Closed at: 2023-11-03 00:21:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2763  

### Version of Beast  
  
This was tested on boost1.81.0-0.  
  
### Steps necessary to reproduce the problem  
  
See the standalone program below.  The code reads the chunks properly if `Transfer-Encoding` header is set to `chunked` by the client.  (See the commented out line where `sample` is set.)  When this header is commented out and `mParser.get().chunked(true)` is set after reading the headers, it does not handle the chunks properly.  
  
In my use case, the client is not sending this header but it's sending chunked body.  (I am emulating an S3 server which does not require the `Transfer-Encoding` to be set to `chunked` when files are uploaded using chunks.  Instead, I have to inspect other headers to determine if chunked mode is being used.)  
  
Is there are way to force the parser to read the body as chunked after the header is read whenever the `Transfer-Encoding` flag is not set?  
  
I am not sure if this is an issue or not.  Please feel free to close if this isn't the proper forum for this.  
  
```cpp  
#include <cassert>  
#include <iostream>  
  
#include <boost/asio/io_context.hpp>  
#include <boost/beast.hpp>  
#include <boost/beast/_experimental/test/stream.hpp>  
  
namespace net = boost::asio;  
namespace beast = boost::beast;  
namespace http = beast::http;  
  
void read_request()  
{  
  net::io_context ioc;  
  
  std::string const payload = "this is a test";  
  
  std::ostringstream sample;  
  
  sample << "PUT /dummy HTTP/1.1\r\n"  
         << "Date: Tue, 05 Apr 2022 13:29:09 GMT\r\n"  
         << "Content-Type: application//json\r\n"  
         //<< "Transfer-Encoding: chunked\r\n"  
         << "\r\n"  
         << std::hex << payload.length() << ";chunk-signature=xxx\r\n"  
         << payload   
         << "\r\n"  
         << "0\r\n\r\n";  // Empty chunk  
  
  
  // Act  
  auto local = beast::test::stream{ioc};  
  auto remote = connect(local);  
  
  auto written = net::write(remote, net::buffer(sample.str()));  
  
  http::request_parser<http::buffer_body> mParser;  
  mParser.eager(false);  
  
  beast::multi_buffer mMultiBuff;  
  read_header(local, mMultiBuff, mParser);  
  
  // **** Since the client is not sending the necessary header, ****  
  // **** try setting the chunked flag to true.                 ****  
  mParser.get().chunked(true);  
  // ***************************************************************  
  
  for (const auto& field : mParser.get()) {  
      std::cout << "HEADER: " << field.name_string() << ":" << field.value() << std::endl;  
  }  
  
  if (mParser.get().chunked())  
  {  
    // change the body type  
    http::parser<true, http::vector_body<char>> p{std::move(mParser)};  
    std::cout << "mParser.chunked()=" << p.chunked() << std::endl;  
    std::cout << "mParser.get().chunked()=" << p.get().chunked() << std::endl;  
    http::read(local, mMultiBuff, p);  
    // done  
  
    auto const& b = p.get().body();  
    std::cout << "Body: " << std::string(b.data(), b.size()) << std::endl;  
  } else {  
     // TODO not chunked  
    std::cout << "Not chunked" << std::endl;  
  }  
}  
  
int main()  
{  
  read_request();  
}  
```  
The output is as expected when the header is included:  
  
```  
HEADER: Date:Tue, 05 Apr 2022 13:29:09 GMT  
HEADER: Content-Type:application//json  
HEADER: Transfer-Encoding:chunked  
mParser.chunked()=1  
mParser.get().chunked()=1  
Body: this is a test  
```  
This the output when the header is missing but the chunked flag is set:  
  
```  
HEADER: Date:Tue, 05 Apr 2022 13:29:09 GMT  
HEADER: Content-Type:application//json  
HEADER: Transfer-Encoding:chunked  
mParser.chunked()=0  
mParser.get().chunked()=1  
Body:   
```  
  
  
  
### All relevant compiler information  
  
This was tested using clang 13.0.0-0.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-11-02 23:16:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2763#issuecomment-1791691510  

That is non-compliant HTTP

---

## Comment 2

> Username: vinniefalco  
> Created at: 2023-11-02 23:17:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2763#issuecomment-1791692537  

This line just changes the message contained in the parser:  
```  
mParser.get().chunked(true);  
```  
  
It does not affect the behavior of the parser. If it did, then Beast would not be compliant with HTTP/1.1. Beast only supports compliant HTTP. If you want non-compliant HTTP then you will need to manually modify the buffer containing the message and insert the "Transfer-Encoding: chunked\r\n" line yourself, before giving it to Beast.  
  
Do you have some documentation on how S3 offers non-standard HTTP that we might look at?

---

## Comment 3

> Username: JustinKyleJames  
> Created at: 2023-11-03 00:21:07 UTC  
> Updated at: 2023-11-03 00:22:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2763#issuecomment-1791734986  

Thanks for the response and I will look into modifying the buffer before reading the header.  
  
Here is the documentation:  
  
https://docs.aws.amazon.com/AmazonS3/latest/API/sigv4-streaming.html  
  
See the note at the top which gives two options one of which does not require Transfer-Encoding.  
  
Note that the client I am currently testing (MinIO) doesn't set either of the two options but that is a different matter.  
  
I will go ahead and close this since it appears my request is out-of-scope since it is not HTTP compliant.
