# #1250 - Documentation Around Parsing Validation Is Vague [Open]

> Username: cmazakas  
> Created at: 2018-09-19 17:59:20 UTC  
> Updated at: 2018-12-21 15:08:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1250  

The documentation around what must be validated when parsing messages is ambiguous in several cases.  
  
For example, nowhere is it documented what the behavior of `http::parser` is in the case of multiple `content-length` headers.  
  
To aid end-users of Beast in using the library effectively and with confidence, it is suggested that all of Beast's HTTP parsing behavior is documented so as to provide clarity around what an end-user must concern themselves with.
