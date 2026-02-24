# #100 - Update string_view docs [Closed]

> Username: Lastique  
> Created at: 2021-10-31 22:00:40 UTC  
> Updated at: 2021-11-01 14:13:15 UTC  
> Closed at: 2021-11-01 14:13:15 UTC  
> Url: https://github.com/boostorg/core/issues/100  

The header path is no longer correct:  
  
https://github.com/boostorg/core/blob/12f5f51427fbc9d27f56e5de002b0008fea420c9/doc/string_view.qbk#L15  
  
Also, it would be a good idea to mention that `boost::core::string_view` is not intended to be used by users (hence it is in `detail`) and is supposed to be used by Boost library writers.

---

## Comment 1

> Username: glenfe  
> Created at: 2021-10-31 22:57:26 UTC  
> Url: https://github.com/boostorg/core/issues/100#issuecomment-955806744  

I don't think `string_view.qbk` is processed (since it isn't included in `core.qbk`) so no HTML etc. is generated for it.

---

## Comment 2

> Username: pdimov  
> Created at: 2021-10-31 22:58:49 UTC  
> Url: https://github.com/boostorg/core/issues/100#issuecomment-955806890  

`string_view.qbk` is no longer included. I just didn't delete it but I will if you insist.

---

## Comment 3

> Username: Lastique  
> Created at: 2021-11-01 01:25:18 UTC  
> Url: https://github.com/boostorg/core/issues/100#issuecomment-955847254  

Sorry, I didn't notice the docs were not included.  
  
I don't insist on removing the docs, just asking to make it actual. If you intend to make it available, even if for library maintainers only, it should point to the right headers and provide guidance to the reader. If you're not going to publish the docs, I don't see the point in keeping them, but I'm fine if you want to keep them for some reason.

---

## Comment 4

> Username: pdimov  
> Created at: 2021-11-01 01:27:47 UTC  
> Url: https://github.com/boostorg/core/issues/100#issuecomment-955848463  

The future of string views is still unclear, so this documentation might still come into use, once we (Boost) figure out what we want and in which library and namespace we want it.

---

## Comment 5

> Username: Lastique  
> Created at: 2021-11-01 01:30:21 UTC  
> Url: https://github.com/boostorg/core/issues/100#issuecomment-955849615  

Ok, feel free to close this if you don't intend to update the docs at the moment.
