# #1419 - Add support for "HTTP-like" protocols [Closed]

> Username: jm-mikkelsen  
> Created at: 2019-01-28 15:29:05 UTC  
> Updated at: 2019-05-06 16:00:38 UTC  
> Closed at: 2019-04-19 01:43:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1419  

There are various "HTTP-like" protocols that exist and are in common use. They are similar but not totally identical. SIP references the HTTP RFC for the message and header field definitions with the qualifier that the character encoding is UTF-8.  
  
Examples include:  
  
* [SIP, RFC 3261](https://tools.ietf.org/html/rfc3261)  
* [RTSP 1.0, RFC 2326](https://tools.ietf.org/html/rfc2326)  
* [RTSP 2.0, RFC 7826 ](https://tools.ietf.org/html/rfc7826)  
  
Others surely exist, I know of some proprietary protocols that borrow the HTTP framework.  
  
There are differences:  
  
* UTF-8 supported vs. ASCII only. SIP and RTSP use UTF-8, HTTP is ASCII.  
* Supporting or not supporting chunked encoding.  
* Requiring or not requiring `content-length` when a response body is present.  
* Connection keepalive rules.  
* UDP support vs streams only  
  
Being able to support these would be useful. Is this something worth considering? Thoughts/suggestions on the approach?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-01-28 17:08:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-458216742  

Yes. In the past I have considered this to be out of scope but as it comes up on a recurring basis it might be time to reconsider.  
  
> UTF-8 supported vs. ASCII only. SIP and RTSP use UTF-8, HTTP is ASCII.  
  
We need to be specific about "UTF-8 supported." Do SIP or RTSP allow UTF-8 code points in the field names? HTTP already allows high-ascii in the field values so we're okay there.  
  
> Supporting or not supporting chunked encoding.  
  
Do you have some supporting references (e.g. links to rfc sections)?  
  
> Requiring or not requiring content-length when a response body is present.  
> Connection keepalive rules.  
  
These can be made customizable without much fuss I believe.  
  
> UDP support vs streams only  
  
Well this is something else entirely. The way to support UDP is to implement a class that meets the requirements of synchronous or asynchronous read and write streams, and use the existing Beast algorithm with that. Do you agree?

---

## Comment 2

> Username: jm-mikkelsen  
> Created at: 2019-01-29 09:40:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-458473077  

> Yes. In the past I have considered this to be out of scope but as it comes up on a recurring basis it might be time to reconsider.  
>   
> > UTF-8 supported vs. ASCII only. SIP and RTSP use UTF-8, HTTP is ASCII.  
>   
> We need to be specific about "UTF-8 supported." Do SIP or RTSP allow UTF-8 code points in the field names? HTTP already allows high-ascii in the field values so we're okay there.  
  
"Supported" is not strong enough. From the SIP RFC, section 7:  
  
    "SIP is a text-based protocol and uses the UTF-8 charset"  
  
So should have said it *is* UTF-8 rather than "supports" UTF-8.  
  
> > Supporting or not supporting chunked encoding.  
>   
> Do you have some supporting references (e.g. links to rfc sections)?  
  
From RFC 3261 (SIP):  
  
     7.4.2 Message Body Length  
  
     The body length in bytes is provided by the Content-Length header  
     field.  Section 20.14 describes the necessary contents of this header  
     field in detail.  
  
     The "chunked" transfer encoding of HTTP/1.1 MUST NOT be used for SIP.  
     (Note: The chunked encoding modifies the body of a message in order  
     to transfer it as a series of chunks, each with its own size  
     indicator.)  
  
> > UDP support vs streams only  
>   
> Well this is something else entirely. The way to support UDP is to implement a class that meets the requirements of synchronous or asynchronous read and write streams, and use the existing Beast algorithm with that. Do you agree?  
  
Yes, I agree. There is a requirement that each UDP message contain exactly one request or reply; applications could check that all data has been consumed after processing a request.

---

## Comment 3

> Username: jm-mikkelsen  
> Created at: 2019-01-29 09:57:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-458478766  

Two additional notes:  
  
### Content-Length  
  
RFC 3261 section 20.14 says `content-length` is only mandatory when using a stream protocol. When using UDP it may be omitted. Once mandatory/non-mandatory `content-length` is possible the application can decide, so shouldn't be a Beast issue.  
  
### Compact forms  
  
SIP supports "compact forms" for common header fields. From the RFC:  
  
    7.3.3 Compact Form  
  
     SIP provides a mechanism to represent common header field names in an  
     abbreviated form.  This may be useful when messages would otherwise  
     become too large to be carried on the transport available to it  
     (exceeding the maximum transmission unit (MTU) when using UDP, for  
     example).  These compact forms are defined in Section 20.  A compact  
     form MAY be substituted for the longer form of a header field name at  
     any time without changing the semantics of the message.  A header  
     field name MAY appear in both long and short forms within the same  
     message.  Implementations MUST accept both the long and short forms  
     of each header name.  
  
These are the compact forms referenced in section 20 of the SIP RFC, extracted with grep:  
  
    The compact form of the Call-ID header field is i.  
    The compact form of the Contact header field is m (for "moved").  
    The compact form of the Content-Encoding header field is e.  
    The compact form of the Content-Length field is l.  
    The compact form of the Content-Type field is c.  
    The compact form of the From header field is f.  
    The compact form of the Subject header field is s.  
    The compact form of the Supported header field is k.  
    The compact form of the To header field is t.  
    The compact form of the Via header field is v.  
  
I do not see compact forms in either RTSP version.

---

## Comment 4

> Username: jm-mikkelsen  
> Created at: 2019-01-29 10:34:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-458490387  

Additional explicit departures from HTTP from reading over the RFCs:  
  
### Version  
  
SIP RFC 3261, section 7.1  
  
           Unlike HTTP/1.1, SIP treats the version number as a literal  
           string.  In practice, this should make no difference.  
  
This is probably true: The string is required to be "SIP/2.0", implementations must send upper case but handle upper and lower case for the "SIP" component.  
  
### Date  
  
SIP RFC 3261, section 20.17, says that `Date` must be an RFC1123 date in the GMT timezone. I do not believe this is a Beast issue.  
  
### UTF-8 vs. ASCII  
  
There is more detail here, as I read the RFCs in detail.  
  
RTSP 2.0 RFC 7826, sections 20.2.1 and 20.2.2 defines:  
  
* The reason phrase in the response status line to be UTF-8.  
* The contents of a `quoted-string` to be UTF-8.  
* The value of an extension header (`header-value`) to be UTF-8.  
  
And from the SIP RFC 3261, section 25.1:  
  
    The TEXT-UTF8 rule is only used for descriptive field contents and  
    values that are not intended to be interpreted by the message parser.  
    Words of *TEXT-UTF8 contain characters from the UTF-8 charset (RFC  
    2279 [7]).  The TEXT-UTF8-TRIM rule is used for descriptive field  
    contents that are n t quoted strings, where leading and trailing LWS  
    is not meaningful.  In this regard, SIP differs from HTTP, which uses  
    the ISO 8859-1 character set.  
  
      TEXT-UTF8-TRIM  =  1*TEXT-UTF8char *(*LWS TEXT-UTF8char)  
      TEXT-UTF8char   =  %x21-7E / UTF8-NONASCII  
      UTF8-NONASCII   =  %xC0-DF 1UTF8-CONT  
                      /  %xE0-EF 2UTF8-CONT  
                      /  %xF0-F7 3UTF8-CONT  
                      /  %xF8-Fb 4UTF8-CONT  
                      /  %xFC-FD 5UTF8-CONT  
      UTF8-CONT       =  %x80-BF  
  
    A CRLF is allowed in the definition of TEXT-UTF8-TRIM only as part of  
    a header field continuation.  It is expected that the folding LWS  
    will be replaced with a single SP before interpretation of the TEXT-  
    UTF8-TRIM value.  
  
And later on in this section:  
  
    The backslash character ("\") MAY be used as a single-character  
    quoting mechanism only within quoted-string and comment constructs.  
    Unlike HTTP/1.1, the characters CR and LF cannot be escaped by this  
    mechanism to avoid conflict with line folding and header separation.

---

## Comment 5

> Username: jm-mikkelsen  
> Created at: 2019-01-29 10:44:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-458493632  

One more note on `Content-Length` from RTSP, RFC 7826, section 18.17:  
  
    The Content-Length message body header field contains the length of  
    the message body of the RTSP message (i.e., after the double CRLF  
    following the last header) in octets of bits.  Unlike HTTP, it MUST  
    be included in all messages that carry a message body beyond the  
    header portion of the RTSP message.  If it is missing, a default  
    value of zero is assumed.  Any Content-Length greater than or equal  
    to zero is a valid value.  
  
My reading of this is that this is another way of saying "no body if no Content-Length".

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-01-29 15:34:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-458585327  

Great stuff. So, what would the top level declaration look like? Right now we have  
```  
template <bool isRequest, class Derived>  
class basic_parser;  
```  
  
Where the `Derived` type has to have certain member functions.  Would we have a different parser for each new protocol?

---

## Comment 7

> Username: p-kalugin  
> Created at: 2019-01-29 17:15:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-458626188  

>   
> Examples include:  
>   
>     * [SIP, RFC 3261](https://tools.ietf.org/html/rfc3261)  
>   
>     * [RTSP 1.0, RFC 2326](https://tools.ietf.org/html/rfc2326)  
>   
>     * [RTSP 2.0, RFC 7826 ](https://tools.ietf.org/html/rfc7826)  
>   
>   
> Others surely exist, I know of some proprietary protocols that borrow the HTTP framework.  
>   
  
Would like to add the SSDP to the list. SSDP is a discovery layer of UPnP stack, and is basically a simplified HTTP/1.1 over multicast UDP. It would be great to be able to handle things like SSDP with such a Beast!

---

## Comment 8

> Username: jm-mikkelsen  
> Created at: 2019-01-29 20:07:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-458688089  

A short comment here, will write more tomorrow. UTC+1 here.  
  
I imagined `Derived`:  
  
* having methods or types to give policy to `basic_parser` for things like: chunked-allowed/chunked-not-allowed or keepalive rules.  
* Having functions to be used by `basic_parser`, for example `parse_version()`.  
  
I am not sure that the top level declaration for `basic_parser` needs to change that much. Of course, they could also be additional template parameters on `basic_parser`.

---

## Comment 9

> Username: jm-mikkelsen  
> Created at: 2019-01-29 20:08:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-458688369  

> Would like to add the SSDP to the list. SSDP is a discovery layer of UPnP stack, and is basically a simplified HTTP/1.1 over multicast UDP. It would be great to be able to handle things like SSDP with such a Beast!  
  
What are the differences in SSDP?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2019-01-29 20:13:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-458689946  

> I am not sure that the top level declaration for basic_parser needs to change that much  
`basic_parser` definitely has to be able to reflect, through the type system, the protocol it is capable of parsing - in a way that is independent of the `Derived` parameter. The derived class should not direct the parsing in any way it should only receive the results.  
  
Authors of stream (or UDP) algorithms need to be able to express in function signatures that the parser expected is of the specific protocol. For example:  
```  
template<class SyncReadStream, class Derived>  
void read_some_sip(  
    SyncReadStream& stream,  
    basic_parser<Derived, SIPTraits>& p);  
```

---

## Comment 11

> Username: djarek  
> Created at: 2019-01-29 23:35:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-458750998  

> Well this is something else entirely. The way to support UDP is to implement a class that meets the requirements of synchronous or asynchronous read and write streams, and use the existing Beast algorithm with that. Do you agree?  
  
Stream is not the appropriate abstraction, because you're dealing with a datagram oriented protocol.  
AFAIK, these HTTP-like-over-UDP protocols are designed in such a way that each message has to fit within a UDP datagram (which may be fragmented). Typically these protocols avoid UDP fragmentation by limiting message sizes below MTU. Example from SIP RFC: https://tools.ietf.org/html/rfc3261#section-18.1.1

---

## Comment 12

> Username: vinniefalco  
> Created at: 2019-01-29 23:54:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-458755279  

Well, that's even easier. There's no `read_some_udp` there's just `read_udp`.

---

## Comment 13

> Username: p-kalugin  
> Created at: 2019-01-30 09:48:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-458879982  

> What are the differences in SSDP?  
  
* UDP based.  
* No body. Body is ignored if present.  
* Only two request methods allowed: ```NOTIFY``` and ```M-SEARCH```.  
* Custom header fields.  
* UTF-8 is used in headers.

---

## Comment 14

> Username: jm-mikkelsen  
> Created at: 2019-02-02 10:36:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-459954641  

I have been taking a closer look at this. I haven't used Beast before, so if I'm not following some convention please let me know.  
  
I see this as a possible approach:  
  
```  
namespace http {  
class protocol  
{  
public:  
    static constexpr string_view name() { return string_view("HTTP"); }  
    static bool check_version(int v) { return v >= 10 && v <= 11; }  
    static bool use_http11_keepalive(int v) { return v >= 11; }  
    static string_view remap_field(string_view name) { return name; }  
    static stringview name_to_string(field name) { return to_string(name); }  
    static constexpr bool allow_chunked() { return true; }  
    static constexpr bool override_content_length() { return false; }   
    static constexpr bool allow_header_utf8() { return false; }  
};  
}  
  
template <bool isRequest, class Derived, class Protocol = protocol>  
class basic_parser;  
  
template<class Allocator, class Protocol = protocol>  
class basic_fields  
```  
  
For other protocols, there is (for example) `namespace sip { template<bool is_tcp> class protocol { ... } }`, with details. The remapping functions are for handling the case where, for example, a header field of "c" and "content-type" are equivalent in the `basic_parser` implementation, and when encoding for UDP with the short form on output in `basic_fields` on calls to `new_element`.  
  
`basic_parser_base::parse_field` needs to learn about UTF-8 as an option. I haven't looked at that in detail yet.  
  
I haven't looked in detail about read and write interfaces for UDP.  Because it's only dealing with one datagram at a time I don't expect that to be hard.  
  
I'm making speculative changes to try this out in principle. Any comments?

---

## Comment 15

> Username: vinniefalco  
> Created at: 2019-02-02 14:21:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-459968930  

> I haven't looked in detail about read and write interfaces for UDP. Because it's only dealing with one datagram at a time I don't expect that to be hard.  
  
Yeah I agree. UDP will probably be something like this  
  
```  
template<  
    class Protocol,  
    bool isRequest, class Body, class Fields>  
std::size_t  
write(  
    net::basic_datagram_socket<Protocol>& sock,  
    serializer<isRequest, Body, Fields>& sr);  
```

---

## Comment 16

> Username: vinniefalco  
> Created at: 2019-02-02 14:30:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-459969502  

As for your `http::protocol`, well... that's one way to do it. Not much finesse there though. Every little thing has to be carved out into a constant or function. I haven't thought about it in depth yet but I have to believe there is an elegant generalization waiting to be discovered.

---

## Comment 17

> Username: jm-mikkelsen  
> Created at: 2019-02-02 15:08:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-459972364  

> As for your `http::protocol`, well... that's one way to do it. Not much finesse there though. Every little thing has to be carved out into a constant or function. I haven't thought about it in depth yet but I have to believe there is an elegant generalization waiting to be discovered.  
  
Some things really are just constants or functions.  
  
Although I agree there could be a more elegant solution, and my claim is more that this is practical than elegant. The elegant solution, is suspect, will involve changing the base Beast structure a bit, and I've spent one day looking at the code and seeing what is possible.

---

## Comment 18

> Username: vinniefalco  
> Created at: 2019-02-02 15:09:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-459972430  

Yep, I agree with all that

---

## Comment 19

> Username: vinniefalco  
> Created at: 2019-02-02 17:36:52 UTC  
> Updated at: 2019-02-02 17:38:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-459983563  

So here is just an idea of the top of my head, `basic_parser` can have a customizable `LineParser` function object which produces a key/value pair, this is what I mean by more generalizable. Beast can expose the default HTTP line parser as a separate type, and then you can re-use that component in your own code so if the `basic_parser` isn't good enough for your needs then you can write your own but still leverage most of what is already there.

---

## Comment 20

> Username: jm-mikkelsen  
> Created at: 2019-02-22 13:12:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-466391856  

@vinniefalco   
  
I'm not sure how   
  
> > I haven't looked in detail about read and write interfaces for UDP. Because it's only dealing with one datagram at a time I don't expect that to be hard.  
>   
> Yeah I agree. UDP will probably be something like this  
>   
> ```  
> template<  
>     class Protocol,  
>     bool isRequest, class Body, class Fields>  
> std::size_t  
> write(  
>     net::basic_datagram_socket<Protocol>& sock,  
>     serializer<isRequest, Body, Fields>& sr);  
> ```  
  
I'm taking a look at this now. A possible variant that has a destination address for the datagram:  
  
```  
template<  
     class DatagramStream,  
     bool isRequest, class Body, class Fields>  
std::size_t  
send_to(  
    DatagramSocket& sock,  
    typename DatagramSocket::endpoint_type& dest,  
    serializer<isRequest, Body, Fields>& sr);  
```  
  
This uses `DatagramStream` instead of `net::basic_datagram_socket<Protocol>`. Views either way?  
  
The next issue in the implementation is that the call to `sock.send()` needs to have the complete datagram.  
  
My reading of the code is that multiple calls to `sr.next()` may be needed. Is there a good way of getting the whole serialized message back from a single call to `sr.next()`, or will changes be needed to serializer?

---

## Comment 21

> Username: jm-mikkelsen  
> Created at: 2019-02-22 13:14:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-466392290  

> So here is just an idea of the top of my head, `basic_parser` can have a customizable `LineParser` function object which produces a key/value pair, this is what I mean by more generalizable. Beast can expose the default HTTP line parser as a separate type, and then you can re-use that component in your own code so if the `basic_parser` isn't good enough for your needs then you can write your own but still leverage most of what is already there.  
  
The issues I've seen so far aren't so much about parsing lines, but about higher-level behaviour. For example, "when is a chunked response allowed?" Not sure about this yet, I can't see how it helps but I'm probably missing something.

---

## Comment 22

> Username: vinniefalco  
> Created at: 2019-02-22 13:55:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-466403918  

> The next issue in the implementation is that the call to `sock.send()` needs to have the complete datagram. My reading of the code is that multiple calls to `sr.next()` may be needed. Is there a good way of getting the whole serialized message back from a single call to `sr.next()`, or will changes be needed to serializer?  
  
Well, if you're just talking about one datagram the easiest thing to do would be to serialize the message to a dynamically allocated buffer first. Then it becomes trivial.

---

## Comment 23

> Username: jm-mikkelsen  
> Created at: 2019-02-22 14:14:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-466409764  

> Well, if you're just talking about one datagram the easiest thing to do would be to serialize the message to a dynamically allocated buffer first. Then it becomes trivial.  
  
Yes, that's true, but then you need to manage the buffer. I was trying to avoid the allocation and copy. The path of least resistance for UDP is then to use Beast as an encoder/decoder, and handle ASIO and memory separately.

---

## Comment 24

> Username: vinniefalco  
> Created at: 2019-02-22 14:21:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-466412159  

Some messages can be serialized in one go, but not all. It depends on the Transfer-Encoding: chunked setting and the type of the body.

---

## Comment 25

> Username: jm-mikkelsen  
> Created at: 2019-02-22 14:26:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-466413642  

OK. A check for non-chunked is possible at compile time for something like SIP. What are the constraints on the body type?

---

## Comment 26

> Username: vinniefalco  
> Created at: 2019-02-22 14:36:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-466417010  

well for example how would you deal with a `file_body`?

---

## Comment 27

> Username: vinniefalco  
> Created at: 2019-02-22 14:36:51 UTC  
> Updated at: 2019-02-22 14:37:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-466417376  

I don't think you can avoid allocating, just accept it and move forward. On the bright side, the allocation is small because datagrams are limited in size. And you can reuse the buffer for subsequent serialization.

---

## Comment 28

> Username: jm-mikkelsen  
> Created at: 2019-02-23 12:10:24 UTC  
> Updated at: 2019-02-23 12:20:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-466643330  

Yes, I hadn't considered `file_body` in this context. I have now accepted allocation.  
  
I am now looking at allowing a shorted version of field names as required for datagrams. For example, in a SIP datagram encoding, "content-length" is encoded as "l". When receiving, both are allowed.  
  
`http::basic_fields::value_type` requires `name` and `sname`, and there is a warning about undefined behaviour when `to_string(name) != sname` in `basic_fields::insert()`. Why have the version with the enum and the string at all? (Or at least: why is that overload public? I can see why it is useful internally.)  
  
I also noticed that you mentioned refactoring `fields` entirely in a comment on another issue. What directions are you thinking there?

---

## Comment 29

> Username: vinniefalco  
> Created at: 2019-02-23 13:08:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-466647172  

I"m refactoring _Fields_ the concept not `fields` the type. Well, more like getting rid of _Fields_ entirely. And I will probably merge `fields` into `header` so they are just one object instead of the current model where it is a base. The functionality isn't changing.  
  
> Why have the version with the enum and the string at all?  
  
Can you please link the overload in question?

---

## Comment 30

> Username: jm-mikkelsen  
> Created at: 2019-02-23 14:14:18 UTC  
> Updated at: 2019-02-23 14:14:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-466654522  

[http::basic_fields::insert (3 of 3 overloads)](https://www.boost.org/doc/libs/1_69_0/libs/beast/doc/html/beast/ref/boost__beast__http__basic_fields/insert/overload3.html)  
  
```  
template<class Allocator, class Protocol>  
void  
basic_fields<Allocator, Protocol>::  
insert(field name,  
    string_view sname, string_param const& value)  
```  
  
In my current test version I have this code at the beginning of the function:  
  
```  
    if (name != field::unknown)  
	sname = Protocol::name_to_compact(name);  
```  
  
Given the first two overloads, it seems like this should be private, especially given the big Undefined Behaviour™ warning.

---

## Comment 31

> Username: vinniefalco  
> Created at: 2019-02-23 14:38:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-466657130  

Ah yes, that overload is there for derivations of `basic_parser` (including user-defined ones):  
https://github.com/boostorg/beast/blob/c82237512a95487fd67a4287f79f4458ba978f43/include/boost/beast/http/parser.hpp#L388

---

## Comment 32

> Username: vinniefalco  
> Created at: 2019-04-19 01:43:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-484741711  

Unfortunately I have to consider this out of scope because I do not have the time to give it the treatment that it deserves. However, the C++ Alliance may consider developing a new library which achieves these other protocols.

---

## Comment 33

> Username: jm-mikkelsen  
> Created at: 2019-04-19 06:17:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-484778795  

I have completed the SIP support work, and it is being used in real applications. I have not submitted it to github yet because there seem to have been a lot of other changes to Beast between 1.69 and 1.70 and I have not had time to look at the effort of merging those changes.  
  
Are you willing to look at a complete pull request?  
  
Separately, I have also added an RFC7235 implementation for parsing www-authenticate header lines, in a similar style to the containers/iterators in the RFC7320 implementation. Is that something you are willing to consider?

---

## Comment 34

> Username: vinniefalco  
> Created at: 2019-04-19 15:16:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-484927747  

No harm in looking, sure!

---

## Comment 35

> Username: jm-mikkelsen  
> Created at: 2019-05-06 12:51:33 UTC  
> Updated at: 2019-05-06 12:52:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-489608813  

Here is the commit with the SIP and RFC7235 work I have done.  
  
https://github.com/jm-mikkelsen/beast/commit/04a2de94b4185e25372191e6542adf54bef1c930  
  
Some issues:  
  
* There are some cases in the code where member initialisation isn't handled correctly, eg `unsigned version{11};`, which should get a default version for the protocol in use.  
* SIP has a new class of 6xx status codes which I have not added.  
* The full "UTF8" support isn't implemented. It is a little unclear from the spec where it really belongs; I haven't dealt with this yet.  
* Based on Beast as at some months ago, there are a bunch of new changes that I need to merge.  
  
Any feedback? Is this something you are interesting in taking further, or do I just fork at this point?

---

## Comment 36

> Username: vinniefalco  
> Created at: 2019-05-06 14:14:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-489636267  

I only see two functions? You don't need to "fork" beast for this, you can just write it as a library which depends on beast.

---

## Comment 37

> Username: jm-mikkelsen  
> Created at: 2019-05-06 14:19:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-489638199  

"Showing  9 changed files with 1,186 additions and 0 deletions."  
  
Should be a little more than two functions ...

---

## Comment 38

> Username: vinniefalco  
> Created at: 2019-05-06 14:21:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-489638657  

Hmm... I see the rest of it now. No idea what happened. Looking...

---

## Comment 39

> Username: vinniefalco  
> Created at: 2019-05-06 14:31:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-489642338  

Very nice code, but this is not something that I can merge. There are a number of obstacles:  
  
1. I plan on doing a very large, breaking refactoring of HTTP at some point next year. The way that the parser, fields, and body work will be changed. _Fields_ the concept might not even exist anymore.  
  
2. I am trying to move away from templates where possible. You can see this in the latest version of beast, there is a growing amount of ordinary functions which are put into ".ipp" files. This allows Beast to be compiled in a "split compilation" mode, where you can make a static library out of a lot of material, improving compilation performance.  
  
3. Reusing parser code should not be achieved by adding a "Protocol" template parameter. Instead, the parser should be decomposed into smaller algorithms which can be re-used via composition. This decomposition is something I need to do later, after we have made decisions about how HTTP will be refactored.  
  
4. We can make the stream algorithms work more easily with your UDP parser by factoring out the required interface into its own abstract base. The tip of master and develop already use an abstract base instead of a template. Note how `http::read_some` no longer depends on the type of the class derived from `basic_parser`:  
  
https://github.com/boostorg/beast/blob/d1dabebd8352ce0e854474837ed11a27cb688edc/include/boost/beast/http/read.hpp#L80  
  
We can take this one step further and make an even smaller abstract interface that only contains the members needed by the stream read algorithms (`got_some`, `is_done`, `put`, and `put_eof`). This will allow the stream algorithms to work more generally.  
  
I suggest you develop this code as its own mini-library independently from beast, and do not change any of the existing beast HTTP algorithms but rather create your own new ones by copying and pasting as a starting point.

---

## Comment 40

> Username: jm-mikkelsen  
> Created at: 2019-05-06 14:50:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-489649361  

I just looked through my commit, and realised that I only committed the new files, not the changes to Beast. This commit has the changes to your existing code:  
  
https://github.com/jm-mikkelsen/beast/commit/6c1c9405223f7ee58137db6589d743072a73dff1  
  
The RFC7235 code is standalone relative to the rest of the changes, and has one function decomposed from the existing RFC7230 code. This is a separate change that I considered showing separately.  
  
I see your point about merging if you are about to refactor. Breaking apart the parser looks like a good approach, but I did not want to make structural changes to what was already there. That's especially true if you're about to do a wholesale refactoring.  
  
I need to take a closer look at how Beast has changed since I started this work; the abstract interface stuff is all new. It also sounds like it might be better to wait for your larger changes before doing too much.

---

## Comment 41

> Username: vinniefalco  
> Created at: 2019-05-06 16:00:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1419#issuecomment-489674787  

The larger changes will probably take at least a year from now.
