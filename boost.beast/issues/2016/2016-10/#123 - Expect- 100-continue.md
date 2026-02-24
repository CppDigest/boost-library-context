# #123 - Expect: 100-continue [Closed]

> Username: vinniefalco  
> Created at: 2016-10-10 15:03:49 UTC  
> Updated at: 2017-05-05 02:16:37 UTC  
> Closed at: 2017-05-05 02:16:37 UTC  
> Url: https://github.com/boostorg/beast/issues/123  

To support 100-continue we need to be able to read and write the message in two parts. First the headers, then the body. For clients, callers should be able to read a response in between sending the request headers (with "Expect: 100-continue). To make this work, we're adding `request_headers` and `response_headers` which represent the message without the attached body. Then later, a complete message is constructed by moving in the headers.  
  
A synchronous server implementation reading a request in two phases might look like this:  
  
```  
void do_parse()  
{  
  parser_v1<true, headers> p0;  
  parse(stream, rb, p0);  
  if(p0.get().method == "GET")  
  {  
    // process the headers, send back 100-continue  
    // ...  
    parser_v1<true, string_body, headers> p1{std::move(p0)};  
    parse(stream, rb, p1);  
    request<string_body, headers> m = p1.release();  
  }  
  else if(p0.get().method == "POST")  
  {  
    // process the headers, send back 100-continue  
    // ...  
    parser_v1<true, file_body, headers> p1{std::move(p0)};  
    parse(stream, rb, p1);  
    request<file_body, headers> = p1.release();  
  }  
}  
```  
  
Note how `p0` is instantiated without specifying a body type. This class is a specialization of `parser_v1`. It will stop parsing just before receiving the body.  
  
`p1` is declared with a concrete body type, and we move-construct from `p0`, thus inheriting the parser state.

---

## Comment 1

> Username: ahmedcharles  
> Created at: 2016-10-10 15:10:03 UTC  
> Url: https://github.com/boostorg/beast/issues/123#issuecomment-252651685  

Is the `boost::filesystem::path path;` relevant to the example?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2016-10-10 15:12:08 UTC  
> Url: https://github.com/boostorg/beast/issues/123#issuecomment-252652232  

> Is the boost::filesystem::path path; relevant to the example?  
  
Hmm...no, removed

---

## Comment 3

> Username: ahmedcharles  
> Created at: 2016-10-10 15:16:12 UTC  
> Url: https://github.com/boostorg/beast/issues/123#issuecomment-252653276  

As a slight aside, one thing that this example makes clear to me is that the `true/false` that is used to distinguish requests from responses should be an enum or hidden behind type aliases or both. Should I create and issue for that, perhaps?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2016-10-10 15:24:46 UTC  
> Url: https://github.com/boostorg/beast/issues/123#issuecomment-252655481  

> one thing that this example makes clear to me is that the true/false that is used to distinguish requests from responses should be an enum or hidden behind type aliases or both  
  
Full disclosure, someone else brought this issue up several months ago.  
  
It seems like needless overhead to introduce three additional identifiers just to give different labels to `true` and `false`. If this was a high level library, I would probably have done it. This only comes up when declaring the parser (which is an advanced use-case) or when writing function signatures that take generic message objects. Do you think its really important?

---

## Comment 5

> Username: ahmedcharles  
> Created at: 2016-10-10 15:30:08 UTC  
> Url: https://github.com/boostorg/beast/issues/123#issuecomment-252656859  

If I only thought of writing code, I'd say no, it's not important. But if I were reading code, I think it's significantly better to not have to remember that true = request and false = response. And well, a template alias wouldn't be hard to provide. I'd probably agree that using an enum is overkill for this, though.

---

## Comment 6

> Username: ahmedcharles  
> Created at: 2016-10-10 15:54:58 UTC  
> Url: https://github.com/boostorg/beast/issues/123#issuecomment-252663269  

The example code is missing the definition of `reqh`. And I just noticed that your proposed commit doesn't have public constructors for `request_headers` or `response_headers`. How does this work for a client that wants to use `Expect: 100-continue`? They should be able to construct `request_headers` and then use that to construct a `message` later?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2016-10-10 16:07:18 UTC  
> Url: https://github.com/boostorg/beast/issues/123#issuecomment-252666490  

> The example code is missing the definition of reqh  
  
Yep, I fixed it. Now it just peeks at the message headers inside the parser to make the decision.  
  
> your proposed commit doesn't have public constructors for `request_headers` or `response_headers`  
  
Hmm, I thought I added them?  
https://github.com/vinniefalco/Beast/blob/6d692b52dfb0871b99661e166f76459b2433b7f4/include/beast/http/message.hpp#L62  
  
Or do you mean `message` constructors which take `request_headers` or `response_headers`? Those are here:  
  
https://github.com/vinniefalco/Beast/blob/6d692b52dfb0871b99661e166f76459b2433b7f4/include/beast/http/message.hpp#L221  
https://github.com/vinniefalco/Beast/blob/6d692b52dfb0871b99661e166f76459b2433b7f4/include/beast/http/message.hpp#L233  
  
> How does this work for a client that wants to use Expect: 100-continue? They should be able to construct request_headers and then use that to construct a message later?  
  
Something like that, Instead of constructing `request_headers` they just construct the full message like normal. In this use-case, the **Body** type would be something that defers its expensive operations to someplace other than the constructor. I haven't worked it out fully. But it would go something like this:  
- Client calls `msg.headers.insert("Expect", "100-continue")`  
- Client calls `beast::http::write`  
- The implementation detects the presence of the Expect header, and serializes everything up to but not including the body. Then it sets `ec = http::error::expect_continue` and returns.  
  
The client checks for the special error code, and then does the following:  
- Set a timer for a timeout (as per https://tools.ietf.org/html/rfc7231)  
- Attempt to read a HTTP response  
  
If the timer expires, or a HTTP response is received indicating "100 Continue", the client continues to send the message body as expected.  
  
I haven't worked out what the second client call to send the message body looks like. The complication, is that there is some state information calculated which we would lose if `write` returns before sending the body. That information is called the `write_preparation` and its declared here:  
https://github.com/vinniefalco/Beast/blob/6d692b52dfb0871b99661e166f76459b2433b7f4/include/beast/http/impl/write.ipp#L83  
  
One way get past this is to just recalculate the state information but that is unsatisfying. There are also questions about what happens to the **Writer** concept. Does it get initialized twice? Can it fail? When does initialization happen? What if the first call to write the headers succeeds but the second call fails?

---

## Comment 8

> Username: ahmedcharles  
> Created at: 2016-10-10 16:16:27 UTC  
> Url: https://github.com/boostorg/beast/issues/123#issuecomment-252668722  

Sorry, I misread the diff and thought the `protected` line stayed, hence my belief that the constructors were protected. Ignore that.

---

## Comment 9

> Username: ahmedcharles  
> Created at: 2016-10-10 16:51:43 UTC  
> Url: https://github.com/boostorg/beast/issues/123#issuecomment-252677277  

I think there are two scenarios for using `Expect: 100-Continue`:  
1. Producing the body is expensive.  
2. Communication over the network is expensive.  
  
In the second case, using a `message` object doesn't impose additional overhead, since a standard `Body` type can be used, rather than writing a custom one. However, in the first case, it's possible that the expensive operation can take advantage of a standard `Body` type.  
  
What if `write` were overloaded to support `message`, `request_headers` and `opaque_type<Body>`? The idea would be that the `opaque_type` would be returned by a call to `write` somehow and could then be used to wrap a `Body` object and then used to call `write` again. It seems that `write_preparation` only need to keep track of `chunked` and `close` between writing the headers and the body. The other fields `sb` and `msg` don't transfer information between writing the headers and the body and the `w` field is not used to write the headers.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2016-10-10 17:11:00 UTC  
> Url: https://github.com/boostorg/beast/issues/123#issuecomment-252681755  

Don't forget, we need the other end of it (the server side). The server needs to receive just the headers. But also, we want the server to be able to choose the **Body** type after the headers have been received.  
  
The overloads sound like a mess, and could be misused.

---

## Comment 11

> Username: ahmedcharles  
> Created at: 2016-10-10 23:57:34 UTC  
> Url: https://github.com/boostorg/beast/issues/123#issuecomment-252776631  

For parsing on the server side, you don't have the issue of maintaining state, since as you plan above, the parser transitions its state. So, this wouldn't actually change the parser use case.  
  
I could design an interface in Rust that could not be misused, though it would be a bit weird for a C++ programmer. In C++, it's very hard to prevent misuse and often, efficient interfaces always have the potential for misuse as a characteristic, though good interfaces encourage good use.  
  
I don't think this is a great way to design it and I think I'd have to see what it looks like being implemented before saying for sure. I do this it's a reasonable attempt to balance all of the concerns involved, but there may be a better way.

---

## Comment 12

> Username: daniele77  
> Created at: 2016-11-18 10:37:04 UTC  
> Url: https://github.com/boostorg/beast/issues/123#issuecomment-261500915  

I agree with @ahmedcharles : it would be so much better if beast indicates request and response using an enum (or a tag) instead of true and false.  
  
The interface of a library is important, regardless of its "level" (why a "low level library" should have an un-readable interface?!). When reading the code, you cannot say if true means "request" or "response"....  
  
Moreover, moving to enum or tags doesn't bring any extra overhead, so no excuses for not improving the interface :-)

---

## Comment 13

> Username: vinniefalco  
> Created at: 2016-11-18 11:20:44 UTC  
> Url: https://github.com/boostorg/beast/issues/123#issuecomment-261509212  

@daniele77 You can always add this to your own code if you desire:  
  
```  
enum class message_type : bool  
{  
  request : true,  
  response : false  
};  
```  
  
This is 100% compatible with the existing interface. And there are already aliases for requests and responses, see:  
https://github.com/vinniefalco/Beast/blob/master/include/beast/http/message.hpp#L401  
  
You would only see a bool if you pass `isRequest` as a template argument. And there, it would have a name. If you feel that strongly about not using `true` and `false` you can add the enum I described above.

---

## Comment 14

> Username: viccpp  
> Created at: 2016-11-18 18:36:40 UTC  
> Updated at: 2016-11-18 18:39:54 UTC  
> Url: https://github.com/boostorg/beast/issues/123#issuecomment-261606936  

`enum class` value can't be used in `bool` context

---

## Comment 15

> Username: vinniefalco  
> Created at: 2016-11-18 18:40:30 UTC  
> Updated at: 2016-11-18 18:44:43 UTC  
> Url: https://github.com/boostorg/beast/issues/123#issuecomment-261607870  

Sorry, this works:  
  
```  
enum message_type : bool  
{  
  request = true,  
  response = false  
};  
  
void foo(bool)  
{  
}  
  
int main()  
{  
    foo(message_type::request);  
}  
```

---

## Comment 16

> Username: viccpp  
> Created at: 2016-11-18 18:42:23 UTC  
> Url: https://github.com/boostorg/beast/issues/123#issuecomment-261608394  

Yes, if  
  
> request : true  
  
means `request = true`  ;-)

---

## Comment 17

> Username: vinniefalco  
> Created at: 2016-11-18 18:44:57 UTC  
> Url: https://github.com/boostorg/beast/issues/123#issuecomment-261609028  

Sorry, updated - and here's a compiling example! http://melpon.org/wandbox/permlink/4xHBlalyqOdNYbur
