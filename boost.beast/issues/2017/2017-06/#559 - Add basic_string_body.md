# #559 - Add basic_string_body [Closed]

> Username: vinniefalco  
> Created at: 2017-06-29 18:49:25 UTC  
> Updated at: 2017-07-04 01:08:09 UTC  
> Closed at: 2017-07-04 01:08:09 UTC  
> Url: https://github.com/boostorg/beast/issues/559  

We need `basic_string_body` to allow choices for `CharT`, `Traits`, and most importantly `Allocator` for tests.

---

## Comment 1

> Username: octopus-prime  
> Created at: 2017-06-29 19:11:16 UTC  
> Url: https://github.com/boostorg/beast/issues/559#issuecomment-312070831  

Hmm...  
Now you want to start making `string_body` "configurable".  
We have "configurable" `string_body`s called `const_body` and `mutable_body`.  
They are more flexible and powerful than the `basic_string_body` which would be **tight bounded** to `std::basic_string`

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-29 19:12:23 UTC  
> Url: https://github.com/boostorg/beast/issues/559#issuecomment-312071143  

I'm also okay with not having it, even if it comes at the expense of a few lines of coverage

---

## Comment 3

> Username: octopus-prime  
> Created at: 2017-06-29 19:18:38 UTC  
> Url: https://github.com/boostorg/beast/issues/559#issuecomment-312072594  

Yes. I think so too.  
If we decide to have configurable string_body we should do the big step and loosely bind to concepts.  
This might be a future task. And may be the formal review will bring new insights.  
So we should wait...

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-07-04 01:08:09 UTC  
> Url: https://github.com/boostorg/beast/issues/559#issuecomment-312757298  

This is superceded by https://github.com/vinniefalco/Beast/issues/580
