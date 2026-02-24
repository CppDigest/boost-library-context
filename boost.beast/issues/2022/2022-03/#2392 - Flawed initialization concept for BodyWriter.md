# #2392 - Flawed initialization concept for BodyWriter [Closed]

> Username: RoiEXLab  
> Created at: 2022-03-01 16:15:27 UTC  
> Updated at: 2022-06-14 18:07:40 UTC  
> Closed at: 2022-06-14 17:37:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2392  

### Version of Beast  
  
1.78.0 (latest)  
  
### The Problem  
  
So I wrote a custom `BodyWriter` that allows to compress data on the fly using the HTTP `Content-Encoding` mechanism. This means the `BodyWriter` has to conditionally set the response headers to `gzip` or `deflate` in this case, based on what the HTTP client actually supports. This is what the current layout currently looks like:  
  
```c++  
class custom_body::writer {  
  value_type& _value;  
  
 public:  
  using const_buffers_type = boost::asio::const_buffer;  
  
  template <bool isRequest, class Fields>  
  writer(boost::beast::http::header<isRequest, Fields>& header, value_type& value):  _value{value} {  
    if (value.isGzip()) {  
      header.insert(field::content_encoding, "gzip");  
    } else if (value.isDeflate()) {  
      header.insert(field::content_encoding, "deflate");  
    }  
  }  
  
  void init(boost::system::error_code& ec) noexcept {  
    ec = {};  
  }  
  
  boost::optional<std::pair<const_buffers_type, bool>> get(boost::system::error_code& ec) {  
    // compressing code  
  }  
};  
```  
  
However when I recently stumbled over [the official documentation of `BodyWriter`](https://www.boost.org/doc/libs/1_78_0/libs/beast/doc/html/beast/concepts/BodyWriter.html) it clearly stated that:  
> The writer shall not access the contents of h or v before the first call to init, permitting lazy construction of the message.   
  
For the constructor of the writer class. The only way to be compliant with this requirement is to use type-erasure of some sort (std::any, std::function, etc.)due to the `template <bool isRequest, class Fields>` being used for the header; as it can't be stored as a regular member. This is really inconvenient because the type information is lost. In case the documentation has a legitimate reason for this requirement, a potential solution to this would be to pass the headers to `init` and/or `get` as an argument directly instead of passing it to the constructor.  
  
What are your thoughts on this?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-03-01 16:19:17 UTC  
> Updated at: 2022-03-01 16:19:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2392#issuecomment-1055615611  

Yes I agree the Body concept (plus the reader and writer) have design flaws. You are probably better just writing the header using beast, but serializing the body yourself using a buffer-based approach instead of trying to make it work with the Body concept.

---

## Comment 2

> Username: madmongo1  
> Created at: 2022-03-01 16:34:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2392#issuecomment-1055630538  

I remember we discussed how to fix this, but the eventual consensus was that it was going to require an invasive redesign.

---

## Comment 3

> Username: RoiEXLab  
> Created at: 2022-03-08 14:31:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2392#issuecomment-1061839488  

> Yes I agree the Body concept (plus the reader and writer) have design flaws. You are probably better just writing the header using beast, but serializing the body yourself using a buffer-based approach instead of trying to make it work with the Body concept.  
  
Yeah, writing the headers 'by-hand' is what I ended up doing. I'm not exactly sure how you imagine a buffer-base approach. Somehow writing to the stream directly I suppose? I ended up using the `Body` concept just to implement chunked transfer encoding (because the size is unknown) so I don't have to change the code that previously sent a plain string too much.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-06-14 17:37:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2392#issuecomment-1155501621  

We can't change this for now but I am working on some new concepts in a new library.

---

## Comment 5

> Username: RoiEXLab  
> Created at: 2022-06-14 17:56:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2392#issuecomment-1155517939  

@vinniefalco By "new library" do you mean something completely independent of beast or more like beast 2.0?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-06-14 18:01:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2392#issuecomment-1155522202  

Completely independent of beast

---

## Comment 7

> Username: RoiEXLab  
> Created at: 2022-06-14 18:07:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2392#issuecomment-1155527431  

@vinniefalco Thanks for your time and I'm looking forward to it 👍🏼
