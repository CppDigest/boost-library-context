# #1143 - operator<< for basic_fields::value_type [Closed]

> Username: vinniefalco  
> Created at: 2018-05-26 16:02:05 UTC  
> Updated at: 2023-12-31 09:09:40 UTC  
> Closed at: 2023-12-31 09:09:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1143  

This would be handy for debugging. Should it append a newline?

---

## Comment 1

> Username: sehe  
> Created at: 2018-05-26 16:19:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1143#issuecomment-392271692  

> This would be handy for debugging  
  
Perhaps. I'm not convinced it's a big issue. I guess it would fit well in the interface for consistency, but really the need for serialization of individual headers seems pretty slim to me. In case one wants to write them, one typically wants them all, as part of a message.  
  
> Should it append a newline?  
  
Nah, CRLF is a protocol-level delimiter of individual headers. Beware of RFC 2616 "Line Folding":  
  
> > HTTP/1.1 header field values can be folded onto multiple lines if the continuation line begins with a space or horizontal tab. All linear white space, including folding, has the same semantics as SP. A recipient MAY replace any linear white space with a single SP before interpreting the field value or forwarding the message downstream.  
  
This got deprecated in RFC 7230 though that doesn't prevent it occurring int HTTP/1.1 conversations
