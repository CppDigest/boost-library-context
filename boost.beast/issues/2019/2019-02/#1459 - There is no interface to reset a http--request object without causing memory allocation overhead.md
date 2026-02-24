# #1459 - There is no interface to reset a http::request object without causing memory allocation overhead. [Closed]

> Username: madmongo1  
> Created at: 2019-02-18 13:23:59 UTC  
> Updated at: 2022-06-16 21:08:01 UTC  
> Closed at: 2022-06-16 16:37:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1459  

PLEASE DON'T FORGET TO "STAR" THIS REPOSITORY :)  
  
Done :)  
  
### Version of Beast  
  
1.69  
  
### Steps necessary to reproduce the problem  
  
When re-using an `http::request<>`.  
  
Given a member `current_request_` of type `http::request<http::string_body>`, the examples suggest writing:  
  
    current_request_ = {};  
  
The problem is that if the previous internal string was large, this will cause an un-necessary deallocation and possible reallocation as the new string grows.  
  
I would prefer to avoid this with something like:  
  
    current_request_.clear();  
  
or  
  
    current_request_.reset();  
  
The former is problematic since although it compiles, it clears only the headers - which gives the impression of an interface that lies.  
  
Until there is a better way, would it be valid to provide:  
  
    void reset(http::request<http::string_body>& req)  
    {  
      req.body().clear();  
      req.clear();  
    }   
  
and then to call `reset(current_request)` in my code?  
  
Going forward, I feel there should be an interface with either or both of the following signatures:  
  
`auto request::reset() -> request&`  
  
and/or  
  
    template<  
        bool isRequest,  
        class Body,  
        class Fields>  
    auto reset(message<isRequest, Body, Fields>& message) -> message<isRequest, Body, Fields>&;  
  
Either approach will require some generic way of resetting the body object in the appropriate manner.  
  
### All relevant compiler information  
  
not relevant.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-02-18 15:32:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1459#issuecomment-464776627  

Yes this is a problem unfortunately. I think your `reset` solution is a good workaround for now. I am planning on getting rid of the _Fields_ concept, and make it so that `message` just has `basic_fields` built-in to it. Then I can make `clear` do something sane, and expand the _Body_ concept so that each body type can implement clear properly.

---

## Comment 2

> Username: djarek  
> Created at: 2019-02-18 16:16:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1459#issuecomment-464794967  

@madmongo1   
Are you're observing performance issues related to this in your code?

---

## Comment 3

> Username: madmongo1  
> Created at: 2019-02-18 16:32:04 UTC  
> Updated at: 2019-02-18 16:34:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1459#issuecomment-464800846  

> Are you're observing performance issues related to this in your code?  
  
@djarek  
  
I am not. I have built a rudimentary `reset()` free function in my code because:  
  
* I feel it's more pleasing to avoid a memory allocation.  
* I feel it is more expressive than `current_request = {};`  
* It's more `grep`able - this turns out to be more important than I first realised.  
  
This is more of a "good design" issue than a critical blocker. I am huge fan of beast and would rather highlight areas where design can be improved early... before the critics turn up ;-)

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-02-18 16:58:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1459#issuecomment-464809761  

I agree with all these points

---

## Comment 5

> Username: djarek  
> Created at: 2019-02-19 00:30:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1459#issuecomment-464931824  

How is a `reset`/`clear` member function supposed to allow avoiding a memory allocation? it would require recycling nodes, which I believe is something that doesn't have a single good solution in a container like `fields`.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-02-19 00:55:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1459#issuecomment-464936145  

I was thinking about adding recycling specifically for this case

---

## Comment 7

> Username: madmongo1  
> Created at: 2019-02-19 06:45:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1459#issuecomment-465006548  

@djarek My primary concern was around the re-use of the body. I had assumed that the `clear()` member of the underlying `fields` container was already efficient. If it is not yet, that's not necessarily important at the present time. It's more of a QoI issue in my mind.  
  
I work on the basis that if I use interfaces correctly, implementation quality will naturally improve over time.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2019-02-19 06:54:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1459#issuecomment-465008844  

The HTTP message container needs a refactoring. This _Fields_ concept never really panned out the way that I hoped it would. And the problem you pointed out, the ambiguity of `clear`, is very real. It was quite difficult to get this container even to where it is now, let alone "perfect." There are other issues with the container as well, in particular with the `buffer_body` type and incremental reading. The `file_body` also has some problems.  
  
The thing needs to be redesigned to address your issue and all the others. The new design will be very similar to the old one, but there will certainly be some backward-incompatibilities. For example I will probably do away with the _Fields_ concept, and merge the fields functionality into the message object itself. This will make it easier to have a consistent `clear` function.

---

## Comment 9

> Username: madmongo1  
> Created at: 2019-02-19 07:17:37 UTC  
> Updated at: 2019-02-19 07:18:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1459#issuecomment-465014420  

Having fields as a subobject of a message type would also mean that customising allocators might be more possible and powerful.  
  
An http request seems an ideal candidate for using an arena allocator for memory management. Ideally you might want the headers and string body to consume memory from an arena incrementally, but discard all memory (and not execute destructors) when the request has been processed.  
  
If this arena were of a fixed maximum size, this could additionally provide an automatic constraint on combined header/body size.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2019-02-19 07:26:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1459#issuecomment-465016704  

You can still customize the allocator for both the fields and the body today, so an arena allocator is already possible.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2022-06-16 16:37:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1459#issuecomment-1157890811  

Yeah this is a problem. Beast's HTTP message model is quite outdated given what we know. This library implements a better model https://github.com/CPPAlliance/http_proto so I am reluctant to invest heavily in Beast anymore.

---

## Comment 12

> Username: sehe  
> Created at: 2022-06-16 20:37:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1459#issuecomment-1158107521  

> I am reluctant to invest heavily in Beast anymore.  
  
Might you specify whether this is particularly relating to beast::http, or beast in general (I think I know the answer, just preventing people getting the wrong impression reading this on quite a number of stale issues now being closed).  
  
I suppose the most straightforward way to balance expectations would be to state a roadmap and goals in the README or so.

---

## Comment 13

> Username: vinniefalco  
> Created at: 2022-06-16 21:08:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1459#issuecomment-1158132053  

I rather keep boost.http.proto low profile until it is robust enough to be usable in at least a beta sense.
