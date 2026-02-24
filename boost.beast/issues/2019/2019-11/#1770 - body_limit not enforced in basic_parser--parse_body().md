# #1770 - body_limit not enforced in basic_parser::parse_body() [Closed]

> Username: nickolasrossi  
> Created at: 2019-11-19 09:22:44 UTC  
> Updated at: 2020-04-07 17:34:27 UTC  
> Closed at: 2020-04-07 17:21:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1770  

Version: 1.68  
Compiler: clang 8 (ubuntu)  
  
Should this method be checking body_limit and fail if it is exceeded? Currently it doesn't:  
  
```  
void  
basic_parser<isRequest, Derived>::  
parse_body(char const*& p,  
    std::size_t n, error_code& ec)  
```  
  
For background, I'm using the "change body type" trick to process requests... where I first `async_read_header()` with an empty_body parser, then based on what I find in the header, create a new parser with a different body type. This is done on a persistent connection, so no eofs.  
https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/more_examples/change_body_type.html  
  
I set body_limit to UINT64_MAX for the initial header parse, since some requests will be routed to buffer_body and I don't want them rejected based on content length.  
  
When the selected body type is string_body, I reset the body_limit to a small number before continuing on to `async_read()` the body. In this case, I found the body_limit was ignored, I could still post a large string. In the debugger I found that the above method `parse_body()` was the only method entered during the body parse, and the running body size was never checked against body_limit.  
  
I thought I was safe in doing this since the body has not begun parsing when I reset body_limit:  
"Setting the limit after any body octets have been parsed results in undefined behavior"  
  
Thank you!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-11-19 12:37:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1770#issuecomment-555488936  

Hmm... the doc is wrong. It should read "Setting the limit after any message octets have been parsed results in undefined behavior." The body limit is ignored in your case because when the body is not chunked, it only checks the Content-Length. Your use-case is interesting though, perhaps we can support it.  
  
The problem is, what do we do if the caller sets `body_limit` after reading the header, and the limit is below the promised Content-Length (and there is no chunked encoding)?

---

## Comment 2

> Username: nickolasrossi  
> Created at: 2019-11-19 22:43:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1770#issuecomment-555750851  

Ah, I see. I think I should check Content-Length on my own after reading the header and selecting a body type. I can error out at that point if it exceeds the new limit.  
  
I suppose the only other way to enforce it is inside the body_limit(..) setter, comparing the new value against existing Content-Length and throwing an error immediately. But then you'd need a `body_limit(v, ec)` variation to return a non-throwing error. Maybe a little messy.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-11-20 02:52:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1770#issuecomment-555812584  

My thoughts exactly. Changing the doc is easiest.

---

## Comment 4

> Username: madmongo1  
> Created at: 2020-04-07 17:21:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1770#issuecomment-610516128  

The documentation does indeed contain the disclaimer:   
  
"Setting the limit after any body octets have been parsed results in undefined behavior."  
  
Closing issue.

---

## Comment 5

> Username: nickolasrossi  
> Created at: 2020-04-07 17:34:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1770#issuecomment-610522337  

The issue was that the doc should say "after any message octets", including both header and body, not just body.
