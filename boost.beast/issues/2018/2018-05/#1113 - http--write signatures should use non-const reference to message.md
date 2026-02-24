# #1113 - http::write signatures should use non-const reference to message [Closed]

> Username: vinniefalco  
> Created at: 2018-05-03 00:34:52 UTC  
> Updated at: 2018-05-09 01:23:19 UTC  
> Closed at: 2018-05-09 01:23:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1113  

`message` arguments to `http::write` should be non-const references to allow messages to be serialized which require that the message is modifiable, such as when using `file_body`.  
  
Perhaps there should be overloads for both const and non-const references. We would need to introduce a trait to determine if the message can be serialized as const (by inspecting the Body).
