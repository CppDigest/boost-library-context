# #577 - Assert if Content-Length is forgotten [Closed]

> Username: vinniefalco  
> Created at: 2017-07-02 21:14:55 UTC  
> Updated at: 2017-08-16 00:26:27 UTC  
> Closed at: 2017-08-16 00:26:27 UTC  
> Url: https://github.com/boostorg/beast/issues/577  

If we add `FieldsReader::content_length()` returning `boost::optional<std::uint64_t>` we can assert on serialization if the user forgot to set the Content Length field in the case where the `Body::size` is available.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-08-16 00:26:27 UTC  
> Url: https://github.com/boostorg/beast/issues/577#issuecomment-322624898  

This still won't work because they could be doing a response to a HEAD request and then the assert would be wrong. There's no good way to do this, unfortunately. caveat emptor.
