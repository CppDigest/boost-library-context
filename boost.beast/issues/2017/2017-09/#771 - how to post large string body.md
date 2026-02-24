# #771 - how to post large string body [Closed]

> Username: haike1363  
> Created at: 2017-09-09 07:22:44 UTC  
> Updated at: 2017-09-10 01:42:08 UTC  
> Closed at: 2017-09-10 01:05:23 UTC  
> Url: https://github.com/boostorg/beast/issues/771  

terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<std::length_error> >'  
  what():  field value too large

---

## Comment 1

> Username: haike1363  
> Created at: 2017-09-09 07:24:20 UTC  
> Url: https://github.com/boostorg/beast/issues/771#issuecomment-328260466  

i can set a large body, but beast can not fit the length automatic?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-09-09 13:42:15 UTC  
> Url: https://github.com/boostorg/beast/issues/771#issuecomment-328278065  

It looks like you are trying to set a header field to a string larger than 64KB - that is too large! Did you mean to set the body instead? Can you provide some example code which demonstrates your problem?

---

## Comment 3

> Username: haike1363  
> Created at: 2017-09-10 01:04:51 UTC  
> Url: https://github.com/boostorg/beast/issues/771#issuecomment-328312855  

thank you！the problem is that you have indicated  
       // request  
        const std::string& body = data_points_[done_ % data_points_.size()];  
        //req_.set(http::field::body, body); // it is the  problem!!!  
        req_.body = body;

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-09-10 01:40:43 UTC  
> Url: https://github.com/boostorg/beast/issues/771#issuecomment-328313952  

Glad to hear you got it sorted, thanks!

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-09-10 01:41:52 UTC  
> Updated at: 2017-09-10 01:42:08 UTC  
> Url: https://github.com/boostorg/beast/issues/771#issuecomment-328313988  

By the way, it looks like you are making a copy of the string. If you know that the string is going to exist at least until the completion handler is called, you can use `request<span_body<char>>` instead of `request<string_body>` and make it a non-owning reference.
