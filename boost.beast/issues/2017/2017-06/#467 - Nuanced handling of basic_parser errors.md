# #467 - Nuanced handling of basic_parser errors [Closed]

> Username: vinniefalco  
> Created at: 2017-06-11 16:44:29 UTC  
> Updated at: 2017-07-04 02:48:14 UTC  
> Closed at: 2017-07-04 02:48:14 UTC  
> Url: https://github.com/boostorg/beast/issues/467  

See:  
https://stackoverflow.com/questions/686217/maximum-on-http-header-values  
  
Users need a way to finish parsing a message even when limits are exceeded, or an element has the wrong syntax. Otherwise, the server has no alternative but to close the connection. In order to return meaningful HTTP Status Responses, the parser has to have a way to keep going. This could mean a `restart` function after an error, or something like that, which discards invalid data on a parse restart.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-04 02:48:14 UTC  
> Url: https://github.com/boostorg/beast/issues/467#issuecomment-312767731  

Actually no, the server should just send a response and close the connection. Servers should assume malformed inputs are malicious.
