# #1460 - Buffer overflow exception during websocket handshake [Closed]

> Username: togga  
> Created at: 2019-02-19 08:57:59 UTC  
> Updated at: 2019-02-22 06:48:27 UTC  
> Closed at: 2019-02-22 06:48:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1460  

Beast version: 181  
  
When trying to connect a websocket to www.cryptofacilities.com, I frequently (but not always) get a buffer overflow exception (`std::length_error`) during the protocol upgrade. The exception is thrown from the function `read_size_or_throw` in file `core/impl/read_size.ipp`.  
  
To pin point the problem I managed to dump the traffic and it seems like the 101 Switching Protocol HTTP response is larger than the static websocket read buffer. Total header size is about 2400 bytes, and rd_buf is 1536. I tried a naive fix just doubling the read buffer size and this made handshake work as expected.  
  
Here is the mitm dump of the switching protocols response:  
  
    << 101 Switching Protocols 0b  
        Date: Mon, 18 Feb 2019 12:48:36 GMT  
        Connection: upgrade  
        Set-Cookie: __cfduid=de1e209833e7f05aaa1044c6d448994761550494116; expires=Tue, 18-Feb-20 12:48:36 GMT; path=/; domain=.cryptofacilities.com; HttpOnly; Secure  
        Upgrade: websocket  
        Sec-WebSocket-Accept: 4a2AYgQx6UAYIkO2e0n92+IJlvk=  
        Feature-Policy: accelerometer 'none'; ambient-light-sensor 'none'; animations 'none'; autoplay 'none'; camera 'none'; document-write 'none'; encrypted-media 'none'; geolocation 'none'; gyroscope 'none'; legacy-image-formats 'none'; magnetometer 'none'; max-downscaling-image 'none'; microphone 'none'; midi 'none'; payment 'none'; picture-in-picture 'none'; unsized-media 'none'; usb 'none'; vr 'none'  
        Referrer-Policy: origin  
        Strict-Transport-Security: max-age=15552000; includeSubDomains; preload  
        X-Content-Type-Options: nosniff  
        Content-Security-Policy: default-src 'none'; manifest-src 'self'; object-src 'self'; child-src 'self' https://www.google.com; font-src 'self' https://use.typekit.net https://maxcdn.bootstrapcdn.com https://fonts.gstatic.com data:; script-src 'self' 'unsafe-inline' 'unsafe-eval' https://ajax.cloudflare.com https://use.typekit.net https://www.googletagmanager.com https://www.google-analytics.com https://www.google.com https://www.googleadservices.com https://googleads.g.doubleclick.net https://www.gstatic.com; connect-src 'self' wss://*.cryptofacilities.com/ws/v1 wss://*.cryptofacilities.com/ws/indices https://uat.cryptofacilities.com https://uat.cf0.io wss://*.cf0.io https://www.googletagmanager.com https://www.google-analytics.com https://www.google.com https://fonts.googleapis.com https://google-analytics.com https://use.typekit.net https://p.typekit.net https://fonts.gstatic.com https://www.gstatic.com https://chart.googleapis.com; worker-src 'self'; img-src 'self' https://chart.googleapis.com https://p.typekit.net https://www.google.co.uk https://www.google.com https://www.google-analytics.com https://stats.g.doubleclick.net data:; style-src 'self' 'unsafe-inline' https://use.typekit.net https://p.typekit.net https://fonts.googleapis.com https://maxcdn.bootstrapcdn.com  
        X-Frame-Options: SAMEORIGIN  
        X-Xss-Protection: 1; mode=block  
        Expect-CT: max-age=604800, report-uri="https://report-uri.cloudflare.com/cdn-cgi/beacon/expect-ct"  
        Server: cloudflare  
        CF-RAY: 4ab09be1a9d0cb06-ARN

---

## Comment 1

> Username: madmongo1  
> Created at: 2019-02-19 09:28:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1460#issuecomment-465055311  

For reference, some information on header size limits in other implementations:  
  
https://stackoverflow.com/questions/686217/maximum-on-http-header-values

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-02-20 23:35:47 UTC  
> Updated at: 2019-02-20 23:35:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1460#issuecomment-465799205  

`101 Switching Protocols 0b` is not a valid status-line shouldn't it read `HTTP/1.1 Switching Protocols 101`?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-02-20 23:44:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1460#issuecomment-465801233  

Confirmed, this is happening in v217:  
```  
// https://github.com/boostorg/beast/issues/1460  
void  
testIssue1460()  
{  
    net::io_context ioc;  
  
    stream<test::stream> ws1(ioc);  
    stream<test::stream> ws2(ioc);  
    test::connect(ws1.next_layer(), ws2.next_layer());  
  
    ws1.next_layer().append(  
        "HTTP/1.1 101 Switching Protocols 0b"  
        "Date: Mon, 18 Feb 2019 12:48:36 GMT"  
        "Connection: upgrade"  
        "Set-Cookie: __cfduid=de1e209833e7f05aaa1044c6d448994761550494116; "  
            "expires=Tue, 18-Feb-20 12:48:36 GMT; path=/; domain=.cryptofacilities.com; HttpOnly; Secure"  
        "Upgrade: websocket"  
        "Sec-WebSocket-Accept: 4a2AYgQx6UAYIkO2e0n92+IJlvk="  
        "Feature-Policy: accelerometer 'none'; ambient-light-sensor 'none'; "  
            "animations 'none'; autoplay 'none'; camera 'none'; document-write 'none'; "  
            "encrypted-media 'none'; geolocation 'none'; gyroscope 'none'; legacy-image-formats 'none'; "  
            "magnetometer 'none'; max-downscaling-image 'none'; microphone 'none'; midi 'none'; "  
            "payment 'none'; picture-in-picture 'none'; unsized-media 'none'; usb 'none'; vr 'none'"  
        "Referrer-Policy: origin"  
        "Strict-Transport-Security: max-age=15552000; includeSubDomains; preload"  
        "X-Content-Type-Options: nosniff"  
        "Content-Security-Policy: default-src 'none'; manifest-src 'self'; object-src 'self'; "  
            "child-src 'self' https://www.google.com; "  
            "font-src 'self' https://use.typekit.net https://maxcdn.bootstrapcdn.com https://fonts.gstatic.com data:; "  
            "script-src 'self' 'unsafe-inline' 'unsafe-eval' https://ajax.cloudflare.com https://use.typekit.net "  
            "https://www.googletagmanager.com https://www.google-analytics.com https://www.google.com https://www.googleadservices.com "  
            "https://googleads.g.doubleclick.net https://www.gstatic.com; connect-src 'self' wss://*.cryptofacilities.com/ws/v1 wss://*.cryptofacilities.com/ws/indices "  
            "https://uat.cryptofacilities.com https://uat.cf0.io wss://*.cf0.io https://www.googletagmanager.com https://www.google-analytics.com https://www.google.com "  
            "https://fonts.googleapis.com https://google-analytics.com https://use.typekit.net https://p.typekit.net https://fonts.gstatic.com https://www.gstatic.com "  
            "https://chart.googleapis.com; worker-src 'self'; img-src 'self' https://chart.googleapis.com https://p.typekit.net https://www.google.co.uk https://www.google.com "  
            "https://www.google-analytics.com https://stats.g.doubleclick.net data:; style-src 'self' 'unsafe-inline' https://use.typekit.net https://p.typekit.net "  
            "https://fonts.googleapis.com https://maxcdn.bootstrapcdn.com"  
        "X-Frame-Options: SAMEORIGIN"  
        "X-Xss-Protection: 1; mode=block"  
        "Expect-CT: max-age=604800, report-uri=\"https://report-uri.cloudflare.com/cdn-cgi/beacon/expect-ct\""  
        "Server: cloudflare"  
        "CF-RAY: 4ab09be1a9d0cb06-ARN");  
    ws2.async_accept(test::success_handler());  
    ws1.async_handshake("test", "/",  
        test::fail_handler(beast::http::error::buffer_overflow));  
    ioc.run();  
}  
```

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-02-21 00:04:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1460#issuecomment-465806159  

The fix will go into v217
