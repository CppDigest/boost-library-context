# #111 Fix handling empty HTTP headers in parser_v1.hpp [Closed]

> Username: rzarzynski  
> Created at: 2016-10-05 22:05:59 UTC  
> Updated at: 2016-10-05 22:37:21 UTC  
> Closed at: 2016-10-05 22:34:54 UTC  
> Url: https://github.com/boostorg/beast/pull/111  

This patch rectifies `flush()` of `beast::http::parser_v1` to properly handle the case when an HTTP header has empty value.  
  
Without the fix an empty-valued HTTP header is being concatenated with the header directly following it. This situation can be replicated using eg. curl:  
  
```  
  curl <url> -H "X-First;" -H "X-Second: bla"  
```  
  
What Beast's client would see is a single header named as `X-FirstX-Second`.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2016-10-05 22:19:21 UTC  
> Url: https://github.com/boostorg/beast/pull/111#issuecomment-251816290  

Confirmed with this regression test:  
  
```  
            error_code ec;  
            parser_v1<true, string_body, headers> p;  
            std::string const s =  
                "GET / HTTP/1.1\r\n"  
                "X1:\r\n"  
                "X2:\r\n"  
                "X3:x\r\n"  
                "\r\n";  
            p.write(buffer(s), ec);  
            if(! BEAST_EXPECTS(! ec, ec.message()))  
                return;  
            BEAST_EXPECT(p.complete());  
            auto const msg = p.release();  
            BEAST_EXPECT(msg.headers.exists("X1"));  
            BEAST_EXPECT(msg.headers["X1"] == "");  
            BEAST_EXPECT(msg.headers.exists("X2"));  
            BEAST_EXPECT(msg.headers["X2"] == "");  
            BEAST_EXPECT(msg.headers.exists("X3"));  
            BEAST_EXPECT(msg.headers["X3"] == "x");  
```  
  
Thanks!! This will go into b15

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2016-10-05 22:29:12 UTC  
> Url: https://github.com/boostorg/beast/pull/111#issuecomment-251818216  

Ah, well I might have spoken too soon. There's a problem with your changes. `on_field` can be called more than once, with pieces of the field name. We need to know when `on_field` is called the first time, or after a call to `on_value`. Working on a fix....

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2016-10-05 22:34:54 UTC  
> Url: https://github.com/boostorg/beast/pull/111#issuecomment-251819276  

This is looking much better!  
https://github.com/vinniefalco/Beast/commit/2fc27fb84e3d4e1a893b71a3f1a9d9f9020f56ed  
  
I'm putting your change into _1.0.0-b15_, which you can track here:  
https://github.com/vinniefalco/Beast/pull/105  
  
Thank you for bringing this to my attention and providing a fix!

---

## Comment 4

> Username: rzarzynski  
> Created_at: 2016-10-05 22:37:21 UTC  
> Url: https://github.com/boostorg/beast/pull/111#issuecomment-251819785  

@vinniefalco: thanks for the extremely quick reaction! :-)

---
