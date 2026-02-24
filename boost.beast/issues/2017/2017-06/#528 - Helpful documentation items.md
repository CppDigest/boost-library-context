# #528 - Helpful documentation items [Closed]

> Username: octopus-prime  
> Created at: 2017-06-22 20:18:55 UTC  
> Updated at: 2017-07-04 01:52:21 UTC  
> Closed at: 2017-07-04 01:52:21 UTC  
> Url: https://github.com/boostorg/beast/issues/528  

I know you are short on time. So this issue has low priority ;-)  
  
As you know a lot of work has to be done  
  
- URLs (or URIs)  
- Cookies  
- Authentication  
- May be multiparts  
- etc.  
  
For this work it needs contributors. Other developers.  
  
So i would like helpful documentation items with  
  
- best practices  
- hints  
- advices  
  
where and how to do this things.

---

## Comment 1

> Username: octopus-prime  
> Created at: 2017-06-22 20:28:16 UTC  
> Updated at: 2017-06-22 21:19:37 UTC  
> Url: https://github.com/boostorg/beast/issues/528#issuecomment-310493398  

Let's have a concrete example.  
  
Somebody wants to work on Authentication. So he knows the plan  
  
1. Send request  
2. Get response  
3. If status 401 and header field "WWW-Authenticate: ..." do an authentication challenge.  
4. Resend request providing the header field "Authorization: ..."  
5. Continue at 2.  
  
What is the best way to do it with beast?  
  
- Writing new `http::read()` functions  
- Using a custom parser and override `on_field()` or `on_header()`  
- etc

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-22 20:31:27 UTC  
> Url: https://github.com/boostorg/beast/issues/528#issuecomment-310494206  

Actually I have a bit of time now, I'm very happy with where the source code is at. You bring up some great points.  
  
You're asking for best practices, hints, and advice for building on top of Beast. And to be honest, I don't really have any good answer for you!  There needs to be experimentation. People need to try writing things like a multi-part encoder or decoder and see how it fits in with Beast.  
  
For the authentication I'm not sure what the best way is. Probably a set of free functions, and maybe a state object to hold some data related to the request or response that is re-used later.  
  
I'd like to hear from some of the other active folks here, any ideas?  
  
Maybe each of these needs to be its own issue?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-06-22 20:32:00 UTC  
> Url: https://github.com/boostorg/beast/issues/528#issuecomment-310494335  

Here's the RFC for Authentication  
https://tools.ietf.org/html/rfc2617

---

## Comment 4

> Username: octopus-prime  
> Created at: 2017-06-22 20:33:18 UTC  
> Url: https://github.com/boostorg/beast/issues/528#issuecomment-310494640  

My favorite for Authentication  
https://tools.ietf.org/html/rfc4559

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-06-22 20:35:50 UTC  
> Url: https://github.com/boostorg/beast/issues/528#issuecomment-310495211  

I'll have a better answer for you in this issue https://github.com/vinniefalco/Beast/issues/529 shortly

---

## Comment 6

> Username: octopus-prime  
> Created at: 2017-06-22 20:41:23 UTC  
> Updated at: 2017-06-22 20:43:00 UTC  
> Url: https://github.com/boostorg/beast/issues/528#issuecomment-310496542  

A 2nd important point: cookies  
Nothing works without session-ids...  
  
- There must be something listening on "Set-Cookie" header fields in reponses  
- There must be something to fill these cookies into requests again  
  
No design/integration idea too...  
  
Something like request-factory ?!  
Some free functions to integrate cookies into requests?!  
etc...

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-06-22 20:43:03 UTC  
> Url: https://github.com/boostorg/beast/issues/528#issuecomment-310496933  

>A 2nd important point: cookies  
  
Right, but that is the responsibility of the server framework, which is outside the scope of Beast. Someone else needs to create a server library on top of Beast, maybe using the code from example/server-framework, and answer these questions about how cookies should be handled. And then the "market" (other programmers) can decide if those answers are the right ones by making that library popular.

---

## Comment 8

> Username: octopus-prime  
> Created at: 2017-06-22 20:45:11 UTC  
> Updated at: 2017-06-22 21:05:55 UTC  
> Url: https://github.com/boostorg/beast/issues/528#issuecomment-310497502  

Server? The client gets "session-id" cookies in responses from server which the client must re-send on next requests...  
So the client needs something like cookiejar.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-06-22 20:45:57 UTC  
> Url: https://github.com/boostorg/beast/issues/528#issuecomment-310497677  

>The client gets "session-id" cookies in responses which must be send on next requests...  
  
That is also beyond the scope of Beast. You're describing behavior that belongs in a client library.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2017-06-22 20:46:41 UTC  
> Url: https://github.com/boostorg/beast/issues/528#issuecomment-310497867  

>So the client needs something like cookiejar.  
  
Like this?  
https://docs.python.org/3/library/http.cookiejar.html

---

## Comment 11

> Username: octopus-prime  
> Created at: 2017-06-22 20:47:41 UTC  
> Url: https://github.com/boostorg/beast/issues/528#issuecomment-310498087  

Yes ;-)

---

## Comment 12

> Username: vinniefalco  
> Created at: 2017-06-22 20:51:23 UTC  
> Url: https://github.com/boostorg/beast/issues/528#issuecomment-310499023  

Cookies might look like this https://github.com/vinniefalco/Beast/issues/530

---

## Comment 13

> Username: vinniefalco  
> Created at: 2017-07-04 01:52:21 UTC  
> Url: https://github.com/boostorg/beast/issues/528#issuecomment-312761673  

I believe this is strictly outside the scope of Beast. That is not to say that we shouldn't have these things, but they should not be part of Beast. Instead, they should be part of a different library with its own tests, CI integration, documentation, and headers. That library would of course use Beast. And that library should eventually be proposed for Boost.
