# #524 - message.body = body and message.prepare_payload() [Closed]

> Username: octopus-prime  
> Created at: 2017-06-21 09:02:41 UTC  
> Updated at: 2017-06-22 17:41:12 UTC  
> Closed at: 2017-06-22 17:41:12 UTC  
> Url: https://github.com/boostorg/beast/issues/524  

See  
http://vinniefalco.github.io/beast/beast/using_http/message_containers.html  
  
There you write  
`  
Note that this message has a body.  
The function message::prepare_payload automatically sets...  
`  
  
What does "has a body" mean?  
Both messages (req and res) in that example have string_body as template argument. So both have a body which is not empty_body.  
But you call prepare_payload() for res only:  
`  
res.prepare_payload();  
`  
This implies that the assignment  
`  
res.body = "Hello, world!";  
`  
makes the message to "have a body". Right?  
  
So the rule is:  
If you assign a body to the message you should call prepare_payload  
?  
  
If so ... have a look at your examples, e.g.  
https://github.com/vinniefalco/Beast/blob/v64/example/http-client/http_client.cpp  
  
As you see there is no assignment of the body but a call of prepare_payload()  
  
That might confuse people.  
  
BTW:  
  
If i set a body (currently via assignment to public member variable body) and i should use prepare_payload() later, why not provide a method to set the body?!  
  
That method will protect the better private body member variable and will call prepare_payload() implicit if needed.  
  
So people can't forget calling prepare_payload().  
So the body member variable could be private.  
So you have control over setting the body.  
  
What do you mean?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-21 11:40:57 UTC  
> Url: https://github.com/boostorg/beast/issues/524#issuecomment-310052595  

Hmm...you are right! The documentation is not clear. `prepare_payload` is an optional function, you don't have to use it. Its not practical to have a member to set the body, because of how broadly the Body concept is specified (how would you "set" a `buffer_body`?) So `body` can't really be private.  
  
Still, you have given me great feedback and I think that the documentation could be better, willfix

---

## Comment 2

> Username: octopus-prime  
> Created at: 2017-06-21 12:23:04 UTC  
> Url: https://github.com/boostorg/beast/issues/524#issuecomment-310061932  

Hmm...  
You have a public member in message  
```  
typename Body::value_type body  
```  
If the member becomes private, why we can't provide a method  
```  
set_body(typename Body::value_type body)  
{  
  this->body = body;  
  this->prepare_payload();  
}  
```  
?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-06-21 14:30:25 UTC  
> Url: https://github.com/boostorg/beast/issues/524#issuecomment-310096790  

`prepare_payload` is not required and in fact sometimes you don't want it. For example, we never want to call `prepare_payload` for this case:  
https://github.com/vinniefalco/Beast/blob/master/example/server-framework/file_service.hpp#L254  
  
Having a `set_body` also imposes some annoying restrictions on `Body::value_type`: It must be movable or copyable, otherwise it cannot be modified. And modification in-place would require moving the value out, performing the modification, and then moving it back in. Consider this code  
```  
template<class DynamicBuffer>  
void foo(DynamicBuffer& buffer)  
{  
  beast::ostream(buffer) << "foo\n";  
}  
  
template<class DynamicBuffer>  
void bar(DynamicBuffer& buffer)  
{  
  beast::ostream(buffer) << "bar\n";  
}  
  
void foobar()  
{  
  response<dynamic_body> res;  
  foo(res.body);  
  bar(res.body);  
}  
```  
  
How would this work with `set_body`?

---

## Comment 4

> Username: octopus-prime  
> Created at: 2017-06-21 16:17:49 UTC  
> Url: https://github.com/boostorg/beast/issues/524#issuecomment-310130593  

1)  
  
In the example  
https://github.com/vinniefalco/Beast/blob/master/example/server-framework/file_service.hpp#L254  
there is no  
`  
res.body = <something>  
`  
So there would be no  
`  
res.set_body(<something>)  
`  
So there would be no implicit  
`  
res.prepare_payload()  
`  
  
2)  
  
The basic idea was: If message has `set_body()` it has `get_body()` too.  
```  
void foobar()  
{  
 response<dynamic_body> res;  
 foo(res.get_body());  
 bar(res.get_body());  
}  
```  
`get_body()` simply returns a reference to the body member variable.  
  
But now I have to think about it!! The bad thing is this  
`  
res.get_body() = <someting>  
`  
or this  
`  
res.get_body() += <someting>  
`  
etc, which changes the body without calling   
`  
res.prepare_payload()  
`  
  
And  
`  
res.get_body() = <someting>  
`  
is not much better than  
`  
res.body = <someting>  
`  
  
I see and will think about it...

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-06-21 16:24:31 UTC  
> Url: https://github.com/boostorg/beast/issues/524#issuecomment-310132434  

>The bad thing is this  
  
Yes :)  
  
I think the current scheme is the best we can do. `message::body` is a simple, public data member. And `message::prepare_payload` is a function that anyone can call, at any time, to adjust the Transfer-Encoding and Content-Length fields as needed in order to reflect the current state of the `body` member. You don't have to call it, you can set those fields manually or not at all. This follows the spirit of Beast's low-level approach.  
  
I do agree that the documentation needs to do a better job explaining this - I am working on it right now!
