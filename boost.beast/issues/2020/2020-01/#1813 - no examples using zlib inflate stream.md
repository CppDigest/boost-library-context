# #1813 - no examples using zlib inflate stream [Open]

> Username: haashirashraf  
> Created at: 2020-01-20 23:23:51 UTC  
> Updated at: 2021-04-27 00:32:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1813  

I'm using beast 1.70.0 and I need to decompress/inflate the contents of the web socket stream, I noticed that there is a native beast inflate stream, I've never used zlib and could not find any examples, how is this inflate stream intended to be used when reading from the web socket stream?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-01-20 23:26:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1813#issuecomment-576459276  

`websocket::stream` already has built-in support for the _permessage-deflate_ extension, and uses zlib automatically. Just turn it on in the settings:  
https://www.boost.org/doc/libs/1_72_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/set_option/overload2.html

---

## Comment 2

> Username: haashirashraf  
> Created at: 2020-01-21 00:51:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1813#issuecomment-576473431  

Thanks for replying so quickly Vinny, I actually tried to do this and set both values to true before calling async_handshake, however I still got binary as a result of calling async_read on the websocket::stream and so I presumed that inflation and deflation were different things, I guess that's not the case, so what am I doing incorrectly?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-01-21 03:11:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1813#issuecomment-576499435  

What does "I still got binary" mean?

---

## Comment 4

> Username: haashirashraf  
> Created at: 2020-01-21 04:00:42 UTC  
> Updated at: 2020-01-21 04:00:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1813#issuecomment-576508361  

Sorry for not clarifying, I mean to say it appears as if despite setting the option permessage-deflate to true for both client (and server, this is probably not necessary here) before calling even async_connect, the bytes being read from the stream are still in a compressed form and are not ascii encoded, I am fairly confident I need to inflate the payloads however there must be something I'm misunderstanding here.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-01-21 04:06:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1813#issuecomment-576509409  

How do you know the server is sending compressed payloads? Can you log the handshake and quote it here?

---

## Comment 6

> Username: haashirashraf  
> Created at: 2020-01-26 01:32:26 UTC  
> Updated at: 2020-01-26 01:32:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1813#issuecomment-578460100  

Apologies for taking so long to get back to you, I resolved the issue by writing a decompressor, I've attached a packet log, change the extension to .plog when you open it. The API docs provided this function for decompressing the payloads using zlib:   
`int gzdecompress(Byte *zdata, uLong nzdata, Byte *data, uLong *ndata)  
{  
    int err = 0;  
    z_stream d_stream = {0}; /* decompression stream */  
  
    static char dummy_head[2] = {  
        0x8 + 0x7 * 0x10,  
        (((0x8 + 0x7 * 0x10) * 0x100 + 30) / 31 * 31) & 0xFF,  
    };  
  
    d_stream.zalloc = NULL;  
    d_stream.zfree = NULL;  
    d_stream.opaque = NULL;  
    d_stream.next_in = zdata;  
    d_stream.avail_in = 0;  
    d_stream.next_out = data;  
  
      
    if (inflateInit2(&d_stream, -MAX_WBITS) != Z_OK) {  
        return -1;  
    }  
  
    // if(inflateInit2(&d_stream, 47) != Z_OK) return -1;  
  
    while (d_stream.total_out < *ndata && d_stream.total_in < nzdata) {  
        d_stream.avail_in = d_stream.avail_out = 1; /* force small buffers */  
        if((err = inflate(&d_stream, Z_NO_FLUSH)) == Z_STREAM_END)  
        break;  
  
        if (err != Z_OK) {  
            if (err == Z_DATA_ERROR) {  
                d_stream.next_in = (Bytef*) dummy_head;  
                d_stream.avail_in = sizeof(dummy_head);  
                if((err = inflate(&d_stream, Z_NO_FLUSH)) != Z_OK) {  
                    return -1;  
                }  
            } else {  
                return -1;  
            }  
        }  
    }  
  
    if (inflateEnd(&d_stream)!= Z_OK)  
        return -1;  
    *ndata = d_stream.total_out;  
    return 0;  
}`  
  
Was it possible to natively decode this using beast?  
  
[packet_log.txt](https://github.com/boostorg/beast/files/4113083/packet_log.txt)

---

## Comment 7

> Username: vinniefalco  
> Created at: 2020-01-26 13:50:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1813#issuecomment-578503715  

Is this resolved? What was the problem?

---

## Comment 8

> Username: haashirashraf  
> Created at: 2020-01-26 15:07:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1813#issuecomment-578510196  

This is resolved now, the problem was that setting per message at the web socket::stream level deflate didn't decode the messages and I had to write my own decompressor, I was wondering if I did something wrong or if this format isn't supported natively by beast?

---

## Comment 9

> Username: vinniefalco  
> Created at: 2020-01-26 16:16:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1813#issuecomment-578517271  

> I was wondering if I did something wrong  
  
Yes, Beast has built-in support for the permessage-deflate extension. It should work.  You provided a log of the messages, but can you provide a log of the WebSocket Upgrade handshake, which happens at the beginning of the connection?

---

## Comment 10

> Username: madmongo1  
> Created at: 2020-01-27 09:10:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1813#issuecomment-578654703  

Hi @haashirashraf are you able to provide a minimal complete verifiable example of the client failing to decompress the compresses frames?  
  
I'd like to run this locally if possible in order to investigate.

---

## Comment 11

> Username: haashirashraf  
> Created at: 2020-02-23 23:15:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1813#issuecomment-590128282  

wow its been a month, sorry for taking so long to reply, I have a private GitHub account for work and I wasn't signed in, I think if you use wss://real.OKEx.com:8443/ws/v3 this url and send this string {"op": "subscribe", "args": ["spot/depth:ETH-USDT"]} with the boost beast ssl websocket client with per message deflate set and it should stream compressed data.

---

## Comment 12

> Username: stale[bot]  
> Created at: 2020-03-24 23:32:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1813#issuecomment-603558240  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 13

> Username: ednolan  
> Created at: 2020-05-07 19:22:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1813#issuecomment-625449127  

Hi,  
I ran into the same issue as `haashirashraf` trying to subscribe to the OKEx WebSocket stream. OKEx says this in the feed documentation:  
  
```  
All the messages returning from Websocket API are optimized by Deflate compression. Users are expected to decompress the messages by their own means(Compression and decompression through the inflate algorithm).  
```  
  
However, what they mean by this is not that they use the `permessage-deflate` extension; instead, they send normal WebSocket messages whose contents are compressed using DEFLATE.  
  
This can be seen in the WebSocket upgrade response headers, which do not acknowledge `permessage-deflate` after we enable it:  
  
```  
HTTP/1.1 101 Switching Protocols  
Date: Thu, 07 May 2020 19:19:40 GMT  
Connection: upgrade  
Set-Cookie: __cfduid=<redacted>; expires=Sat, 06-Jun-20 19:19:39 GMT; path=/; domain=.okex.com; HttpOnly; SameSite=Lax  
upgrade: websocket  
sec-websocket-accept: <redacted>  
CF-Cache-Status: DYNAMIC  
Expect-CT: max-age=604800, report-uri="https://report-uri.cloudflare.com/cdn-cgi/beacon/expect-ct"  
Server: cloudflare  
CF-RAY: 58fd4b3acafa99ea-EWR  
cf-request-id: 02922d58bf000099eaff07a200000001  
```  
  
Like OP, I wound up using zlib and adding a dummy zlib header to the raw DEFLATE data.

---

## Comment 14

> Username: vinniefalco  
> Created at: 2020-05-07 19:26:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1813#issuecomment-625450764  

OKEx is doing it wrong.

---

## Comment 15

> Username: ednolan  
> Created at: 2020-05-07 19:27:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1813#issuecomment-625451173  

I agree. The wonders of crypto APIs.

---

## Comment 16

> Username: ashjas  
> Created at: 2020-05-25 10:20:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1813#issuecomment-633499998  

@vinniefalco i dont see any examples of deflate extention support for http requests.  
Beast doesent have that support?  
I am looking for gz compression in HTTP REST requests.  
If its supported, can you point me to some example usage?  
Thanks.

---

## Comment 17

> Username: vinniefalco  
> Created at: 2020-05-25 14:40:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1813#issuecomment-633603409  

There are no examples, because it is not supported. You need to handle that yourself. Beast provides the zlib code (in <boost/beast/zlib/*> for this purpose.

---

## Comment 18

> Username: sergey-at-ark  
> Created at: 2021-04-27 00:32:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1813#issuecomment-827225454  

Okex V3 API requires websocket messages to be decompressed by the application. By the way, C example of decompression in Okex documentation is buggy - don't use it for production.  
Okex V5 API, which became available recently, supports permessage-deflate: https://www.okex.com/academy/en/complete-guide-to-okex-api-v5-upgrade
