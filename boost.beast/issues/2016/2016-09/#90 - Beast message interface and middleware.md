# #90 - Beast message interface and middleware [Closed]

> Username: e-fominov  
> Created at: 2016-09-25 10:43:14 UTC  
> Updated at: 2016-09-30 09:37:06 UTC  
> Closed at: 2016-09-30 09:37:06 UTC  
> Url: https://github.com/boostorg/beast/issues/90  

I am searching C++11 library for embedding http server inside my application, and I found Beast as a perfect solution. I have found that current message interface is very good and offers full control over every parth of message  
  
What I dont like:  
1. I can forget to fill some fields (status, reason...) and message will be sent with no errors on server side  
2. Too many steps to fill data. Current message is good for low-level operations, but after I tried Nodejs, I want to write less code... This can be done with constructing messages from pre-defined templates or setting some missed fields with default values. <br>I would like to send a file/json answer/custom html or any other typical message with using only one line of code. Yes, I can make such templates myself and use them, but I think that Beast should show users how to make it the right way  
   <br>And do we really need an explicit call of prepare() ?  
  
One more question is not clear for me about message construction and its structure. How to make middleware? For example, something like [connect-multiparty](https://www.npmjs.com/package/connect-multiparty)  
This middleware should be able to add new field "files" into message structure, check request path and intercept messages. Should I derive from request_v1 and write overloaded functions for reading/wiriting? Do you have any plans about middleware affecting message structure? I think, Beast should offer a simple way how to write and use middleware

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-09-25 11:49:24 UTC  
> Updated at: 2016-09-25 11:50:11 UTC  
> Url: https://github.com/boostorg/beast/issues/90#issuecomment-249417290  

First of all thank you for your interest in Beast! Those are some great questions. Answers:  
  
Adding checks or defaults for missing fields would incur a run-time penalty. It is presumed that callers correctly fill out the `message` structure before invoking HTTP algorithms. I could add some asserts though, but that would only help debug builds.  
  
Beast HTTP is intentionally a low level library. From http://vinniefalco.github.io/beast/beast/http/scope.html  
  
_"This library is designed to be a building block for creating higher level libraries. It is not designed to be end-user facing. There is no convenient class that implements the core of a web server, nor is there a convenient class to quickly perform common operations such as fetching a file or connecting and retrieving a document from a secure connection. These use-cases are important, but this library does not try to do that. Instead, it offers primitives that can be used to build those user-facing algorithms."_  
  
I agree that very common use cases require more lines than usual. It is my hope that other developers will use Beast as a building block to create those user-facing algorithms. For example, someone suggested that having a C++ library modeled after Python Requests (http://docs.python-requests.org/en/master/) would be helpful. I hope that someone will build that library on top of Beast.  
  
The explicit call to `beast::http::prepare` is not required. It is just a convenience function that fills in some of the fields for you. For example, it will set the Content-Length field, Transfer-Encoding, and Connection fields appropriately. If you want to set those fields yourself and skip the call to `prepare`, you can do it.  
  
To extend the message structure, it is best to use composition. Create a new `struct` or `class` that has the message as a data member. Custom algorithms that take this new type as a parameter can be in their own namespace, and call Beast algorithms as required. I'm not sure if an example of "using middleware" is warranted - such an example would serve no purpose other than to inform readers of how to use object composition, which is assumed knowledge.
