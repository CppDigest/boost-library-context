# #838 Version 128 [Merged]

> Username: vinniefalco  
> Created at: 2017-10-26 01:49:33 UTC  
> Updated at: 2017-10-29 16:29:41 UTC  
> Merged at: 2017-10-26 15:28:40 UTC  
> Closed at: 2017-10-26 15:28:41 UTC  
> Url: https://github.com/boostorg/beast/pull/838  

* Update doc links  
  
HTTP:  
  
* Add message::need_eof  
* Use message::need_eof in example servers  
  
API Changes:  
  
* Remove serializer::keep_alive  
* Remove serializer::chunked  
* Add has_content_length_impl to Fields  
* Add message::has_content_length  
* Rename some basic_parser observers  
  
Actions Required:  
  
* Call message::keep_alive instead of serializer::keep_alive  
* Call serializer::get::chunked instead of serializer::chunked  
* Implement has_content_length_impl for user-defined Fields  
* Remove the "is_" prefix from call sites invoking certain basic_parser members

---

## Review 1 [Approved]

> Username: nbougalis  
> Created_at: 2017-10-26 02:12:02 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/beast/pull/838#pullrequestreview-72062078  

:+1:  
  
Left a single comment about a sentence that could be imporved.

📁 doc/qbk/07_concepts/Fields.qbk

```diff
 163 |         ]]
 164 |+ 
 165 |+         If the result of adjusting the field value produces an empty
```

> Username: nbougalis  
> Created_at: 2017-10-26 02:03:34 UTC  
> Updated_at: 2017-10-26 15:13:49 UTC  
> Url: https://github.com/boostorg/beast/pull/838#discussion_r147031096  

This reads weird. Prefer:  
  
>If adjusting the field value results in an empty string, the field is removed from the container.  
  
Or  
  
>If the adjusted field value is empty, the field is removed from the container.

> Username: vinniefalco  
> Created_at: 2017-10-26 02:13:44 UTC  
> Updated_at: 2017-10-26 15:13:49 UTC  
> Url: https://github.com/boostorg/beast/pull/838#discussion_r147032020  

Actually I wrote it that way on purpose. There is no requirement that implementations of **Fields** store strings at all. It should actually read:  
  
*If the adjusted field value would produce an empty string upon serialization, the container behaves as if the field is removed*


---
