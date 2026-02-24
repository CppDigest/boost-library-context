# #84 - Documentation [Closed]

> Username: vinniefalco  
> Created at: 2016-09-21 15:16:19 UTC  
> Updated at: 2016-09-29 18:31:38 UTC  
> Closed at: 2016-09-29 18:31:38 UTC  
> Url: https://github.com/boostorg/beast/issues/84  

Replace the main documentation page with a custom written index, like Asio. This will hide the rather ugly auto-generated list of refs below the Quick Reference page.  
  
WebSocket doc changes:  
- Each `stream` member function needs to have as a precondition, that the NextLayer represents an active, valid connection  
- Typo, "approached" in http://vinniefalco.github.io/beast/beast/types/DynamicBuffer.html  
- Some code snippets need brace style constructions  
- Exposition should link to the `stream::handshake` member on http://vinniefalco.github.io/beast/beast/websocket/handshaking.html  
- This page is confusing http://vinniefalco.github.io/beast/beast/websocket/messages.html  
- Some Javadocs incorrectly state that "pongs are noted" when they are actually routed to the pong callback if set.  
- Incomplete sentence on this page http://vinniefalco.github.io/beast/beast/websocket/pongs.html  
- `stream` remarks should read "A stream object must not be moved or destroyed while there are pending asynchronous operations associated with it"  
- In the last code snippet for WebSocket/Handshaking, the call to beast::http::read is missing the sb parameter.  
  
HTTP doc changes:  
- `read` and `write` descriptions should present the functions as generic serialization and deserialization algorithms.  
- **Parser** concept should use **ConstBufferSequence** not **ConvertibleToConstBuffer** in `write` (http://vinniefalco.github.io/beast/beast/types/Parser.html)  
  
General doc changes:  
- Refer to TLS instead of SSL  
- Consider renaming beast/websocket/ssl.hpp to tls.hpp  
- Return type for **DynamicBuffer::prepare** missing a colon (http://vinniefalco.github.io/beast/beast/types/DynamicBuffer.html)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-09-25 18:04:21 UTC  
> Url: https://github.com/boostorg/beast/issues/84#issuecomment-249436152  

Use def macros to replace links

---

## Comment 2

> Username: vinniefalco  
> Created at: 2016-09-29 18:31:38 UTC  
> Url: https://github.com/boostorg/beast/issues/84#issuecomment-250552188  

Fixed in b14
