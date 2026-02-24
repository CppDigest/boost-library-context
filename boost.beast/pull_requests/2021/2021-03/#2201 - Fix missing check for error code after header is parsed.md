# #2201 Fix missing check for error code after header is parsed [Closed]

> Username: haubenmi  
> Created at: 2021-03-18 20:16:56 UTC  
> Updated at: 2021-03-29 17:40:26 UTC  
> Closed at: 2021-03-29 17:40:26 UTC  
> Url: https://github.com/boostorg/beast/pull/2201  

The missing error check leads to completely ignoring the body limit,  
as the body limit is compared to the "Content-Length" header inside  
the "finish_header" method.  
  
This is my first pull request to beast, or boost in general. Please let me   
know if it works for you, or if you prefer me opening an issue instead.  
We found this issue as a regression when we wanted to upgrade from   
Boost 1.72.0 to 1.75.0

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2021-03-18 20:27:31 UTC  
> Url: https://github.com/boostorg/beast/pull/2201#issuecomment-802268804  

Needs a test :)

---

## Comment 2

> Username: haubenmi  
> Created_at: 2021-03-18 20:35:50 UTC  
> Url: https://github.com/boostorg/beast/pull/2201#issuecomment-802276808  

I feared as much :) I will look into how beast does its testing, and hopefully update the request over the weekend 👍

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2021-03-18 20:55:23 UTC  
> Url: https://github.com/boostorg/beast/pull/2201#issuecomment-802291839  

Add a function called `testIssue2201` here  
https://github.com/boostorg/beast/blob/develop/test/beast/http/basic_parser.cpp#L1549

---

## Comment 4

> Username: haubenmi  
> Created_at: 2021-03-21 17:05:46 UTC  
> Url: https://github.com/boostorg/beast/pull/2201#issuecomment-803623876  

I added a test case as you asked. I had to tweak the `test_parser.hpp` a little bit, as the bug only occurs because the real implementation resets the error code in the `on_body_init_impl`.  
I tried to keep the diff minimal, but I think conceptually the `ec = {};` should be added to all `on_*` hooks in `test_parser.hpp`.

---

## Comment 5

> Username: madmongo1  
> Created_at: 2021-03-21 17:07:16 UTC  
> Url: https://github.com/boostorg/beast/pull/2201#issuecomment-803624200  

Many thanks!

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2021-03-21 17:10:15 UTC  
> Url: https://github.com/boostorg/beast/pull/2201#issuecomment-803624829  

@madmongo1 Before you merge this, please edit the commits from the contributor to have the proper email address.

---
